# watchermen

watchermen 作为一个进程管理器，主要职责如下：

- 管理一个或多个子程序，包括子程序的运行、停止、状态检查、配置文件更新并重新加载
- 与服务端保持连接，并接受纳管
- 使用 CGROUP 限制子进程的内存和 CPU
- 上报服务运行指标
- 负责 agent 包自动更新升级

## 启动命令

```shell
watchermen -c /path/to/watchermen_config.json
```

## 配置文件

参考[配置文件](config.md)。
