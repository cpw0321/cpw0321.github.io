# docker知识

## 一、基础

### 1、常用命令

+ 删除命令  

```text
删除所有的镜像 -f强制
docker rmi `docker images -q`

停止所有的容器
docker stop $(docker ps -a -q)

删除所有容器
docker rm $(docker ps -a -q)

```

### 2、安装
+ docker 在ubuntu下安装
```text
1、添加Docker官方GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
（国内阿里云版 sudo curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add -）

2、添加稳定版repository

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

3、sudo apt-get update

4、sudo apt-get install docker-ce docker-ce-cli containerd.io

5、验证：docker --version
sudo docker run hello-world

```

+ 阿里云加速器
```text
sudo mkdir -p /etc/docker
sudo vi /etc/docker/daemon.json
{
"registry-mirrors": ["https://18zqb719.mirror.aliyuncs.com"]
}

sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 3、命令参数
+ -d 后台运行
+ -v 挂载卷
+ -p <宿主机端口>:<容器端口>

### 4、镜像制作
dockerfile

选择基础镜像,可以在hub.docker.com官方去搜索  
+ 使用小包，推荐的：alpine, busybox（scratch是一个空的Docker镜像）  
```text
FROM golang:1.18
```

+ 多阶段构建，编译和创建镜像分开



### 5、示例
```text
FROM golang:1.17.11 AS builder

LABEL stage=gobuilder

ENV CGO_ENABLED 1
ENV GOOS linux
ENV GOPROXY https://goproxy.cn,direct

WORKDIR /build/

COPY . .
#ADD go.mod .
#ADD go.sum .
#RUN go mod download

RUN CGO_ENABLED=1 go build -ldflags="-s -w" -o /app/app main.go

FROM ubuntu:18.04

RUN apt-get update && apt-get install -y locales ca-certificates tzdata && mkdir -p /app/etc/
ENV TZ Asia/Shanghai

WORKDIR /app
COPY --from=builder /app/app /app/app
COPY etc/config.toml /app/etc/config.toml
EXPOSE 8080
CMD ["./app", "-f", "/app/etc/config.toml"]
```

环境变量注入  
```text
CMD envsubst < /app/etc/api.template.yaml > /app/etc/api.yaml && ./api
```
envsubst < input_file > output_file  



---
## 问答
### 1、docker 中 run 和 start 的区别是什么  
+ docker run 相当于执行了两步操作：将镜像放入容器中（docker create）,然后将容器启动，使之变成运行时容器（docker start）  
+ docker start 的作用是，重新启动已存在的镜像  

### 2、add和copy区别  
+ add 支持通过URL从远程服务器读取资源并复制到镜像中, ADD指令更擅长读取本地tar文件并解压缩
+ copy 只能从执行docker build所在的主机上读取资源并复制到镜像中

### 3、run、cmd和entrypoint
+ run 只在docker build时执行，一般用于下载安装包。
+ cmd dockerfile有多条CMD指令时，只有最后一条命令生效；
  "docker run"指定的参数能够覆盖CMD指令的参数；
+ entrypoint dockerfile有多条ENTRYPOINT指令时，只有最后一条命令生效；
  "docker run"指定的参数无法覆盖ENTRYPOINT指令的参数；

### 4、docker和虚拟机区别
```text
虚拟机：虚拟机是通过Hypervisor(虚拟机管理系统，常见的有VMWare workstation、VirtualBox)，虚拟出网卡、cpu、内存等虚拟硬件，再在其上建立虚拟机，每个虚拟机是个独立的操作系统，拥有自己的系统内核。
容器：容器是利用namespace将文件系统、进程、网络、设备等资源进行隔离，利用cgroup对权限、cpu资源进行限制，最终让容器之间互不影响，容器无法影响宿主机。
```
+ docker属于进程之间的隔离，虚拟机可实现系统级别隔离


---