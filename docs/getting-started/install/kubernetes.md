# Kubernetes部署

## 部署Nova

请确保本地有kubectl和helm可执行命令。

- kubectl（[下载](https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG)）
- helm（[下载](https://helm.sh/docs/intro/install/)）

### 下载helm-chart包

```bash
helm pull https://github.com/nova-io/installa tion/releases/download/v1.2.0/nova-v1.2.0.tgz && tar xvzf nova-v1.2.0.tgz
```

### 修改配置
进入chart目录：
```bash
cd installation/nova
```

查看values.yml，并根据实际情况进行修改。  

目前可配置的参数如下所示：

### NovaServer

#### NovaServer镜像
```yaml
server:
  image: "docker.1panel.live/aronic/trace-agent:20241214-8"
```
NovaServer的镜像。

#### 资源
```yaml
server:
    resources:
      limits:
        cpu: 2
        memory: 2Gi
      requests:
        cpu: 100m
        memory: 100Mi
```
nova Agent的limit/request资源，可以根据实际情况修改。

#### 额外启动参数
```yaml
server:
  extraArgs: {}
```
nova的额外启动参数，比如希望修改配置使用debug的日志级别，不使用json格式的日志打印形式，可以修改为:
```yaml
server:
    extraArgs:
      log.level: debug
      log.jsonFormat: false
```

#### 调度
```yaml
server:
    nodeSelector: {}
    
    affinity: {}
    # podAntiAffinity:
    #   requiredDuringSchedulingIgnoredDuringExecution:
    #   - labelSelector:
    #       matchExpressions:
    #       - key: app
    #         operator: In
    #         values:
    #         - nova
    #     topologyKey: "kubernetes.io/hostname"
```
可使用nodeSelector和affinity来控制novaServer Pod的调度，具体请参考Kubernetes文档。

```yaml
tolerations: []
# - effect: NoExecute
#   operator: Exists
# - effect: NoSchedule
#   operator: Exists
```
如果节点有自己的taints，会导致nova Pod无法调度到该节点，如果需要忽略taints，可以加上对应的tolerations。

#### 更新策略
```yaml
updateStrategy:
  type: RollingUpdate
```
可为`RollingUpdate`或者`OnDelete`。

#### 全局配置
```yaml
  app:
  aes_key: "6c84fb94eb046c0876f98777be46d6be"
  debug: true
  server:
    address: ":8080"
    openapiPath: "/api.json"
    swaggerPath: "/swagger"
  grpc:
    address: ":10050"
    server_list:
      - 127.0.0.1:10051
      - 127.0.0.1:10052
      - 127.0.0.1:10053

  closeToken: true
  logger:
    level: "all"
    stdout: true

  database:
    default:
      link:    "mysql:root:root@tcp(localhost:3306)/deep_observe?loc=Local&parseTime=true"
      charset: "utf8mb4"
      debug: "true"
      dryRun: "false"
      maxIdle:  "10"
      maxOpen:  "10"
      maxLifetime:  "30"
      type: mysql
    clickhouse:
      link: "clickhouse:default:asd1234567@@tcp(81.71.98.26:9090)/otel"
      debug: true

  trace-database:
    username: "default"
    password: "asd1234567@"
    database: "otel"
    type: "clickhouse"
    networks:
      - host: "localhost"
        port: 9090
```
具体参数说明可参考[组件配置](../../reference/index.md)。


#### service
如果novaServer希望接收其他服务发送的数据，需要将自身的服务通过service暴露出来。

正常情况下，使用Agent模式的nova只需要暴露自身管理端口。

```yaml
server:
    servicePorts:
      - name: nova-server
        port: 8080
        targetPort: 8080
```

### NovaAgent

#### NovaAgent镜像
NovaTrace 和 NovaCollector 共同使用同一个镜像。
```yaml
agent:
  image: "docker.1panel.live/aronic/trace-agent:20241214-8"
```
NovaAgent的镜像。


#### NovaTrace

##### 资源
```yaml
trace:
    resources:
      limits:
        cpu: 2
        memory: 2Gi
      requests:
        cpu: 100m
        memory: 100Mi
```
nova Agent的limit/request资源，可以根据实际情况修改。

##### 额外启动参数
```yaml
trace:
  extraArgs: {}
```
nova的额外启动参数，比如希望修改配置使用debug的日志级别，不使用json格式的日志打印形式，可以修改为:
```yaml
trace:
    extraArgs:
      log.level: debug
      log.jsonFormat: false
```

##### 调度
```yaml
trace:
    nodeSelector: {}
    
    affinity: {}
    # podAntiAffinity:
    #   requiredDuringSchedulingIgnoredDuringExecution:
    #   - labelSelector:
    #       matchExpressions:
    #       - key: app
    #         operator: In
    #         values:
    #         - nova
    #     topologyKey: "kubernetes.io/hostname"
```
可使用nodeSelector和affinity来控制novaServer Pod的调度，具体请参考Kubernetes文档。

```yaml
trace:
  tolerations: []
# - effect: NoExecute
#   operator: Exists
# - effect: NoSchedule
#   operator: Exists
```
如果节点有自己的taints，会导致nova Pod无法调度到该节点，如果需要忽略taints，可以加上对应的tolerations。

##### 更新策略
```yaml
trace:
    updateStrategy:
      type: RollingUpdate
```
可为`RollingUpdate`或者`OnDelete`。

##### Trace全局配置
```yaml
{
  "cluster": "default",
  "company_uuid": "e902c1e84b9f9b12bdf22936603acfda",
  "node_report_addr": {
    "host": "81.71.98.26",
    "port": 10050
  },
  "sampling_period": 2,
  "oltp_exporter_addr": {
    "host": "81.71.98.26",
    "port": 4317
  },
  "k8s_cri_unix_domain_socket": "/run/containerd/containerd.sock",
  "log_level": "debug",
  "log_file": "/tmp/trace-agent.log",
  "prometheus_exposer_addr": "0.0.0.0:8000"
}
```
具体参数说明可参考[组件配置](../../reference/index.md)。


##### service
如果novaServer希望接收其他服务发送的数据，需要将自身的服务通过service暴露出来。

正常情况下，使用Agent模式的nova只需要暴露自身管理端口。

```yaml
trace:
    servicePorts:
      - name: nova-trace-agent
        port: 11800
        targetPort: 11800
```

#### NovaCollector

##### 资源
```yaml
collector:
    resources:
      limits:
        cpu: 2
        memory: 2Gi
      requests:
        cpu: 100m
        memory: 100Mi
```
nova Agent的limit/request资源，可以根据实际情况修改。

##### 额外启动参数
```yaml
collector:
  extraArgs: {}
```
nova的额外启动参数，比如希望修改配置使用debug的日志级别，不使用json格式的日志打印形式，可以修改为:
```yaml
collector:
    extraArgs:
      log.level: debug
      log.jsonFormat: false
```

##### 调度
```yaml
collector:
    nodeSelector: {}
    
    affinity: {}
    # podAntiAffinity:
    #   requiredDuringSchedulingIgnoredDuringExecution:
    #   - labelSelector:
    #       matchExpressions:
    #       - key: app
    #         operator: In
    #         values:
    #         - nova
    #     topologyKey: "kubernetes.io/hostname"
```
可使用nodeSelector和affinity来控制novaServer Pod的调度，具体请参考Kubernetes文档。

```yaml
collector:
  tolerations: []
# - effect: NoExecute
#   operator: Exists
# - effect: NoSchedule
#   operator: Exists
```
如果节点有自己的taints，会导致nova Pod无法调度到该节点，如果需要忽略taints，可以加上对应的tolerations。

##### 更新策略
```yaml
collector:
    updateStrategy:
      type: RollingUpdate
```
可为`RollingUpdate`或者`OnDelete`。

##### Collector全局配置
```yaml
{
  "cluster": "default",
  "company_uuid": "e902c1e84b9f9b12bdf22936603acfda",
  "node_report_addr": {
    "host": "81.71.98.26",
    "port": 10050
  },
  "sampling_period": 2,
  "oltp_exporter_addr": {
    "host": "81.71.98.26",
    "port": 4317
  },
  "k8s_cri_unix_domain_socket": "/run/containerd/containerd.sock",
  "log_level": "debug",
  "log_file": "/tmp/trace-agent.log",
  "prometheus_exposer_addr": "0.0.0.0:8000"
}
```
具体参数说明可参考[组件配置](../../reference/index.md)。


#### Nova部署

初次部署，我们指定部署在`nova` namespace下，并让helm自动创建该namespace。
```bash
helm install nova ./ -nnova --create-namespace
```

如果你的环境中已经创建了`nova` namespace，可以忽略其中的`-nnova`和`--create-namespace`参数。当然，你也可以使用自己的namespace，将其中`nova`替换即可。  


### 查看部署状态
执行完后，通过使用helm命令来查看部署状态：
```
helm list -nnova
```
类似如下所示：
```
NAME  	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART        	APP VERSION
nova	nova   	1       	2021-11-30 18:06:16.976334232 +0800 CST	deployed	nova-v0.1.0	v0.1.0
```

同时也可以通过kubectl命令查看Pod是否已经被创建。  
```
kubectl -nnova get po
```
类似如下所示：
```
nova-sxxwh   1/1     Running   0          5m21s   10.244.0.5   kind-control-plane   <none>           <none>
```


