# 编译构建

## 持久化引擎选择

NovaServer使用golang的go mod进行包管理，编译。

## 容器镜像构建
默认情况下，Nova的会给所有的main分支构建镜像，并推送到[dockerhub](https://hub.docker.com/r/loggieio/loggie/)上。但是从dockerhub上拉镜像可能存在被限流等问题，所以推荐大家自行构建镜像，或者重新推送到自己的镜像仓库中。

Nova项目下提供了两个Dockerfile:

- Dockerfile：默认的dockerfile，使用了sqlite，所以也会使用CGO构建
- Dockerfile.badger：可在docker build的时候使用`-f`参数指定使用该dockerfile，其中使用了badger作为持久化引擎，存粹的Golang，无CGO。

除了使用docker build外，还可使用make来构建：
```bash
make image TAG=xxx
```
推送镜像：
```bash
make image.push TAG=v1.x.x
```



**多架构**

NovaServer项目中的Dockerfile均支持多架构的构建。dockerhub上的Nova镜像为amd64和arm64的多架构，拉取时会自动识别本地架构。

不过如果你希望修改tag并重新推到自己的仓库，请勿直接docker pull & docker tag & docker push，这会只push你本地架构的镜像，导致仓库中的镜像多架构失效。  
如果希望推送多架构镜像到自己的仓库，可使用其他的一些开源工具，比如[regctl](https://github.com/regclient/regclient/blob/main/docs/regctl.md)，可在本地使用类似`regctl image copy loggieio/loggie:xxx <YourRepoImage>`的命令来推送。

如果接入自己的构建流程或者工具，可以修改配置文件hack/config.yaml

```
gfcli:
  docker:
    build: "-a amd64 -s linux -p temp -ew"
    tagPrefixes:
      - my.image.pub/my-app
```

参考地址:

```
https://goframe.org/docs/cli/docker
```

## 二进制构建
二进制可用于在主机部署的场景。进入cmd/manager下面使用

- build:
  ```bash
  make build
  ```
