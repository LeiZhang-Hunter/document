# watchermen 配置

## 字段解释

| 字段         | 类型                        | 说明                                                |
| ------------ | --------------------------- | --------------------------------------------------- |
| company_uuid | string                      | 公司唯一标识                                        |
| daemon       | bool                        | 是否以守护进程方式运行, 设置为 true 则在后台运行    |
| version      | string                      | 配置版本                                            |
| log_level    | string                      | 日志级别, 分别支持: trace, debug, info, warn, error |
| log_path     | string                      | 日志路径, 有两个特殊值: 'syslog'和'stdout'          |
| network      | [network](#network)         | 网络配置                                            |
| service      | [service](#service)         | 服务配置                                            |
| http_server  | [http_server](#http_server) | http 服务配置                                       |

### network

| 字段 | 类型   | 说明 |
| ---- | ------ | ---- |
| host | string | 主机 |
| port | int    | 端口 |

### service

| 字段           | 类型              | 说明        |
| -------------- | ----------------- | ----------- |
| process_name   | string            | 进程名      |
| command        | string            | 启动命令    |
| cgroup         | [cgroup](#cgroup) | cgroup 配置 |
| config         | string            | 配置        |
| config_path    | string            | 配置路径    |
| config_version | string            | 配置版本    |

### cgroup

| 字段    | 类型   | 说明      |
| ------- | ------ | --------- |
| enabled | bool   | 是否启用  |
| memory  | int    | 内存限制  |
| cpu     | double | cpu 限制  |
| name    | string | cgroup 名 |

### http_server

| 字段          | 类型                            | 说明         |
| ------------- | ------------------------------- | ------------ |
| host          | string                          | 主机         |
| port          | int                             | 端口         |
| health_config | [health_config](#health_config) | 健康检查配置 |

### health_config

| 字段 | 类型   | 说明 |
| ---- | ------ | ---- |
| path | string | 路径 |

### 配置文件样例

```json
{
  "company_uuid": "107057afc5769cbeea96766334f7e7ec",
  "daemon": true,
  "version": "0.0.1",
  "log_level": "debug",
  "log_path": "syslog",
  "network": {
    "host": "127.0.0.1",
    "port": 10050
  },
  "service": [
    {
      "process_name": "xrd-test-process",
      "command": "sleep 111111",
      "cgroup": {
        "enabled": true,
        "memory": 100,
        "cpu": 0.5,
        "name": "/sleep"
      },
      "config": "",
      "config_path": "/usr/etc/conf",
      "config_version": "1.0"
    }
  ],
  "http_server": {
    "host": "0.0.0.0",
    "port": 11900,
    "health_config": {
      "path": "/health"
    }
  }
}
```
