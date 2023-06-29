## 前言

从之前的文章《[构建 Docker 镜像](build.md)》，我们简单构建了第一个自己的 docker 镜像，今天我们就来动手学一下构建 Golang 开发程序的 docker 镜像。

## 准备工作

为了方便操作，我用 gin 写好了一个 hello world 的程序，仓库地址是：

[https://github.com/ErikXu/golang-ci](https://github.com/ErikXu/golang-ci)

安装 git 并克隆代码：

``` bash
yum install git -y

git clone https://github.com/ErikXu/golang-ci.git
```

![克隆代码](images/golang/1.png "克隆代码")

## 镜像构建

进入代码目录：

``` bash
cd golang-ci
```

### 编译可执行文件

使用以下指令编译可执行文件：

``` bash
# 编译可执行文件
docker run --rm -i \
    -v /go:/go \
    -v ${PWD}:/workspace \
    golang:1.16-alpine \
    sh -c 'cd /workspace && go mod tidy && GOOS=linux GOARCH=amd64 GO111MODULE=on CGO_ENABLED=0 go build --ldflags="-s" -v -o ./publish/app'

# 查看可执行文件
ls publish
```

![编译可执行文件](images/golang/2.png "编译可执行文件")

![编译可执行文件](images/golang/3.png "编译可执行文件")

指令解析：

| 项目 | 说明 |
| ------- | ------- |
| docker run | 启动 docker 容器 |
| --rm | 退出容器时销毁容器 |
| -i | 容器交互模式 |
| -v /go:/go | 把宿主机的 /go 目录映射到容器目录 /go，重用已下载过的包 |
| -v ${PWD}:/workspace | 把宿主机当前目录映射到容器目录 /workspace |
| golang:1.16-alpine | 使用 golang 1.16 镜像 |
| sh -c cd /workspace && ... | 启动指令，进入 /workspace 目录执行 go mod tidy 下载依赖包，执行 go build 编译可执行文件 |

### 构建镜像

使用以下指令把可执行文件打入镜像：

``` bash
docker build --no-cache --disable-content-trust=true -t golang-ci:1.0.0 -f ./docker/Dockerfile ./publish/
```

![构建镜像](images/golang/4.png "构建镜像")

![构建镜像](images/golang/5.png "构建镜像")

指令解析：

| 项目 | 说明 |
| ------- | ------- |
| docker build | 构建 docker 容器 |
| -t golang-ci:1.0.0 | 设置镜像名称为 golang-ci:1.0.0 |
| --no-cache | 不使用镜像缓存 |
| --disable-content-trust=true | 禁用内容信任 |
| -f ./docker/Dockerfile | 设置 Dockerfile 的路径为 ./docker/Dockerfile |
| ./publish/ | 指定构建目录为 ./publish/ |

其中 Dockerfile 内容如下：

``` dockerfile
# 使用 alpine 作为基础镜像
FROM alpine:3.14

# 设置 Gin Mode 为 release
ENV GIN_MODE release

# 拷贝可执行文件到 /app
COPY . /app

# 设置 /app 为工作目录
WORKDIR /app

# 操作系统使用阿里云的源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

# 安装 tini
RUN apk update
RUN apk add tini

# 暴露 8080 端口
EXPOSE 8080

# 使用 tini 模式启动程序
ENTRYPOINT ["tini", "--"]
CMD ["/app/app"]
```

## 镜像运行

使用以下指令运行镜像：

``` bash
# 运行镜像
docker run --name golang-ci -d -p 8080:8080 golang-ci:1.0.0

# 查看运行情况
docker ps
```

![运行镜像](images/golang/6.png "运行镜像")

运行效果：

![运行效果](images/golang/7.png "运行效果")

## 总结

按照上述的操作，今天应该能成功完成 Golang 镜像构建的相关操作。

如有问题可以添加公众号【跬步之巅】进行交流。

![跬步之巅](/images/qrcode.gif "跬步之巅")
