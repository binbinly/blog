## 安装

### 文档

[官方文档](https://docs.docker.com/engine/install/)

## 命令介绍

```bash
docker -h

Common Commands:
  run         Create and run a new container from an image
  exec        Execute a command in a running container
  ps          List containers
  build       Build an image from a Dockerfile
  pull        Download an image from a registry
  push        Upload an image to a registry
  images      List images
  login       Log in to a registry
  logout      Log out from a registry
  search      Search Docker Hub for images
  version     Show the Docker version information
  info        Display system-wide information
Management Commands:
  builder     Manage builds
  buildx*     Docker Buildx (Docker Inc., v0.10.5)
  compose*    Docker Compose (Docker Inc., v2.18.1)
  container   Manage containers
  context     Manage contexts
  image       Manage images
  manifest    Manage Docker image manifests and manifest lists
  network     Manage networks
  plugin      Manage plugins
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes
Commands:
  attach      Attach local standard input, output, and error streams to a running container
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  import      Import the contents from a tarball to create a filesystem image
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  wait        Block until one or more containers stop, then print their exit 
```

## 镜像操作
```bash
# 搜索镜像
docker search nginx
# 从registry拉取镜像
docker pull nginx:latest
# 从registry仓库提交镜像
docker push demo:latest
# 查看本地镜像
docker images
# 使用Dockerfile创建镜像 -f 指定 dockerfile 路径 默认当前目录
docker build -t demo:1.0 -f Dockerfile .
# 删除一个或多个镜像
docker rmi demo:1.0 demo:2.0
# 删除未标记或未用过的镜像
docker image prune
# 删除未使用过的镜像
docker image prune -a
# 给镜像加标记
docker tag demo:1.0 newdemo
# 把镜像保存为.tar文件
docker save demo:1.0 > demo_v1.tar
# 从.tar文件载入镜像
docker load -i demo_v1.tar
```

## 容器操作
```bash
# 创建容器
docker create --name nginx_1 -it -p 8080:80 nginx:latest
# 创建 + 运行容器
docker run --name nginx_1 -it -p 8080:80 nginx:latest

# 各选项含义
# -i:以交互模式运行容器，通常与-t 同时使用；
# -d:后台运行容器，并返回容器ID；
# -p:端口隐射, 宿主机在前，容器在后
# -P:随机映射宿主机端口
# -t:为容器重新分配一个伪输入终端，通常与-i 同时使用；
# -v:目录挂载
# --entrypoint: 指定进入点
# --restart=always: 服务重启

# 启动容器
docker start nginx_1
docker stop nginx_1
docker restart nginx_1
docker kill nginx_1 #【杀死】一个运行中的容器
# 查看正在运行中的容器 -a 全部，包含停止的容器
docker ps -a
# 容器重命名
docker rename nginx_1 nginx_2
# 删除一个容器 -f 强制删除
docker rm nginx_1
# 删除已停止运行的所有容器
docker container prune
# 拷贝文件，从容器到宿主机
docker cp nginx_1:/index.html index.html
# 拷贝文件，从宿主机到容器
docker cp index.html nginx_1:/index.html 
# 进入运行的容器
docker exec -it nginx_1 /bin/bash
docker exec -it nginx_1 /bin/bash start.sh
# 查看容器端口映射
docker port nginx_1
# 查看容器内已修改文件
docker diff nginx_1
# 查看容器日志
docker logs nginx_1
# 查看容器内运行进程
docker top nginx_1
# 查看容器的底层信息
docker inspect nginx_1
# 从容器创建一个新的镜像。 -m 提交时的说明文字 -a :提交的镜像作者
docker commit -m="first" -a="binbinly" <id> demo/mynginx:v1.0.0
```

## Dockerfile 详解

| 命令	| 描述	| 示例 |
| ---- | ---- | ---- |
| FROM	| 指定基础镜像	| FROM golang:1.20-alpine |
| MAINTAINER	| 镜像创建者	| MAINTAINER binbinly |
| WORKDIR	| 指定工作目录	| WORKDIR /go/src/app |
| COPY	| 添加宿主机文件到容器，复制	| COPY . . |
| ADD	| 添加宿主机文件到容器，复制+解压	| ADD myfile.tar . |
| ENV	| 设置环境变量	| ENV APP_PATH /go/src/app |
| RUN	| 创建镜像时要执行的命令	| RUN go build -o app . |
| USER	| 切换执行后续命令的用户和用户组, 但这个用户必需首先已使用RUN的命令进行创建好了。	| RUN groupadd -r www && useradd -r -g www www; USER www(切换用户) |
| CMD	| 容器启动时默认要运行的程序。如果执行 docker run 后面跟启动命令会被覆盖掉。	| CMD [“/bin/bash”] |
| ENTRYPOINT	| 同CMD，但其不会被覆盖，可以和docker run命令传递的参数进行拼接执行。	| 如果设置：ENTRYPOINT [“nginx”, “-c”] ， 运行$ docker run nginx_1 -c /etc/nginx.conf => nginx -c /etc/nginx.conf |
| VOLUME	| 定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。	| VOLUME /tmp |
| EXPOSE	| 容器暴露端口，供link到当前容器或通过docker network的容器，不会和宿主机端口映射关系。	| EXPOSE 8080 8081|

+ 构建一个golang app demo

```bash
FROM golang:1.20-alpine as builder

# 作者
LABEL maintainer="gin-chat"

# 镜像设置必要的环境变量
ENV GO111MODULE=on \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64 \
    GOPROXY="https://goproxy.cn,direct" \
    TZ=Asia/Shanghai

# 执行镜像的工作目录
WORKDIR /go/src/gin-chat

# 将目录拷贝到镜像里
COPY . .

RUN go build -o gin-chat .

# 引入alphine最小linux镜像
FROM alpine

# 解决时区问题: unknown time zone Asia/Shanghai
RUN apk update && apk add tzdata

WORKDIR /app
# 复制生成的可执行命令和一些配置文件
COPY --from=builder  /go/src/gin-chat/gin-chat .
COPY --from=builder  /go/src/gin-chat/configs/prod ./configs

# 开放http ws端口
EXPOSE 9050 9060

# 启动执行命令
ENTRYPOINT ["/app/gin-chat"]
```

## 网络操作

```bash
# 创建网络。默认bridge模式。
docker network create –-driver bridge network-web
# 查看已创建的network列表
docker network ls  
# 查看网络详情
docker network inspect network-web
# 删除网络
$ docker network rm network-web
```

### 容器连接到网络

```bash
# --network 表示这个容器要连接到的网络
# --network-alias 表示这个容器在此网络中的名称，也可以使用--ip来指定容器的ip
docker run --name=nginx_1 -d -p 80:80 --network=network-web --network-alias=web nginx:latest
# 将已经在运行的容器加入网络 --ip=192.10.36.122 指定ip地址
docker network connect --alias=web --network=network-web nginx_1
# 断开网络连接
$ docker network disconnet network-web nginx_1
```

## 私有仓库搭建

通过官方提供的私有仓库镜像registry来搭建私有仓库
```bash
docker pull registry:latest
```

### 可视化工具 Harbor

- [项目](https://github.com/goharbor/harbor)
- [安装文档](https://goharbor.io/docs/2.9.0/install-config/)
- [Docker实战](https://github.com/Snailclimb/JavaGuide/blob/main/docs/tools/docker/docker-in-action.md)
- [Docker核心概念总结](https://github.com/Snailclimb/JavaGuide/blob/main/docs/tools/docker/docker-intro.md)