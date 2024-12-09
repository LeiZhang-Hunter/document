# 后端配置

一个Pipeline对应一个Sink。

    Concurrency相关配置使用可参照[自适应sink流量控制](../../../user-guide/best-practice/concurrency.md)。

## 后端Server通用配置

!!! example


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
            charset: "utf8mb4" #数据库编码
            debug: "true"
            dryRun: "false"
            maxIdle:  "10"
            maxOpen:  "10"
            maxLifetime:  "30"
            type: mysql
    clickhouse:
        link: "clickhouse:default:asd1234567@@tcp(81.71.98.26:9090)/otel"
        debug: true

    ```

### app

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | aes_key | string  |    必填    |   1  | 登录信息加密秘钥 |
    | debug | boolean  |    非必填    |   false  | 是否开启收集器grpc的debug模式 |

### server

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | address |   |    必填    |    | sink发送数据给下游时，数据使用的格式 |
    |   openapiPath | string  |    非必填    |   json | codec类型 |
    |   swaggerPath | bool  |    非必填    |  false  | 是否打印出events到Loggie的日志中，可用于临时排查问题 |

#### grpc

    容器数据、进程数据、cpu拓扑图收集器配置。

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | address |  string |    必填    |  :10050  | 数据收集器端口 |
    | server_list | array  |    必填    |  127.0.0.1:10051  | 集群其他服务器列表 |

#### closeToken

    是否关闭token验证

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | closeToken |  boolean |    必填    |  true  | 是否关闭token验证 |

#### logger

    日志配置

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | level |  string |    必填    |  all  | 日志级别 |
    | stdout |  boolean |    必填    |  false  | 是否进行标准输出 |

#### database

    日志配置

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | link |  string |    必填    |  all  | 日志级别 |
    | charset |  string |    必填    |  false  | 是否进行标准输出 |
    | debug |  boolean |    必填    |  false  | 是否进行标准输出 |
    | dryRun |  boolean |    必填    |  false  | 是否进行标准输出 |
    | maxIdle |  int |    必填    |  false  | 是否进行标准输出 |
    | maxOpen |  int |    必填    |  false  | 是否进行标准输出 |
    | maxLifetime |  int |    必填    |  false  | 是否进行标准输出 |
    | type |  string |    必填    |  mysql  | 数据库 |

#### clickhouse

    |    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
    | ---------- | ----------- | ----------- | --------- | -------- |
    | link |  string |    必填    |  clickhouse:default:asd1234567@@tcp(127.0.0.1:9090)/otel  | 日志级别 |
    | debug |  boolean |    必填    |  false  | 是否进行标准输出 |



