# 简介

NavoServer是一个基于Golang的轻量级、高性能、云原生可观测系统的后台API和数据收集，对外提供了GRPC收集指标以及提供http接口能力，提供了：

- :hammer:  **可观测数据收集查询解决方案**：同时可以采集主机负载、进程容器信息以及拓扑信息等数据，对外提供数据
- :cloud: **云原生的日志形态**：快速便捷的容器指标采集方式，原生的Kubernetes动态配置下发
- :key: **生产级的特性**：NavoServer吸收了我们长期的大规模运维经验，形成了全方位的可观测性、快速排障、异常预警、自动化运维能力.



## 构建

参考 [构建文档](build.md) 构建 nova-server.

```shell
./nova-server -c -gf.gcfg.file={$path}/config.yaml
```

## 配置

参考 [配置文档](config.md) 配置 nova-server.

## 上报内容

参考 [上报内容文档](report/index.md) 查看 npva-server 上报的内容.

