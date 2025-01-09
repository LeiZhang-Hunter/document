# 主机部署

## NovaServer

Server使用Golang编译成二进制，可根据自身需求对接各类部署系统。  
这里我们提供一个使用systemd部署NovaServer的参考。  

### 前置检查
- 操作系统：Linux
- 系统架构：amd64
- 发行版支持systemd

目前release仅包含GOOS=linux GOARCH=amd64生成的二进制可执行文件。其他系统和架构，请自行基于源码交叉编译。

### 下载二进制

```
mkdir -p /opt/nova/server && curl https://github.com/nova-io/installation/releases/download/v1.2.0/nova-linux-amd64 -o /opt//server/-server && chmod +x /opt//server/-server
```

## 添加配置文件

请根据实际需求创建配置，以下为参考：

#### 创建config.yml

!!! example "config.yml"

    ```yaml
    cat << EOF > /opt/nova/server/config.yml
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
    EOF
    ```


#### 运行

!!! example "nova-server"

    ```
    nova-server -c config.yaml
    ```

## 添加systemd配置

```yaml
cat << EOF > /lib/systemd/system/nova-server.service
[Unit]
Description=NovaServer

[Service]
MemoryMax=400M
ExecStart=/opt/nova/server/nova-server -c /opt/nova/server/config.yaml
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

## 启动

首先生效配置：
```bash
systemctl daemon-reload
```

然后设置为开机启动：
```bash
systemctl enable nova-server
```

接着就可以正式启动NovaServer了：

```bash
systemctl start nova-server
```

启动后，你可以随时查看进程状态：
```bash
systemctl status nova-server
```

## NovaTraceServer

TraceServer使用Golang编译成二进制，可根据自身需求对接各类部署系统。  
这里我们提供一个使用systemd部署NovaTraceServer的参考。

### 前置检查
- 操作系统：Linux
- 系统架构：amd64
- 发行版支持systemd

目前release仅包含GOOS=linux GOARCH=amd64生成的二进制可执行文件。其他系统和架构，请自行基于源码交叉编译。

### 下载二进制

```
mkdir -p /opt/nova/trace && curl https://github.com/nova-io/installation/releases/download/v1.2.0/nova-linux-amd64 -o /opt//server/-server && chmod +x /opt/nova/trace/trace-server
```

## 添加配置文件

请根据实际需求创建配置，以下为参考：

#### 创建config.json

!!! example "config.json"

    ```yaml
    cat << EOF > /opt/nova/server/config.yml
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
    EOF
    ```


#### 运行

!!! example "trace-server"

    ```
    trace-server -c config.json
    ```

## 添加systemd配置

```yaml
cat << EOF > /lib/systemd/system/trace-server.service
[Unit]
Description=NovaServer

[Service]
MemoryMax=400M
ExecStart=/opt/nova/trace/trace-server -c /opt/nova/trace/config.yaml
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

## 启动

首先生效配置：
```bash
systemctl daemon-reload
```

然后设置为开机启动：
```bash
systemctl enable nova-server
```

接着就可以正式启动NovaServer了：

```bash
systemctl start nova-server
```

启动后，你可以随时查看进程状态：
```bash
systemctl status nova-server
```
