# docker知识

## 1、常用命令

+ 删除命令  

```text
删除所有的镜像 -f强制
docker rmi `docker images -q`

停止所有的容器
docker stop $(docker ps -a -q)

删除所有容器
docker rm $(docker ps -a -q)

```

## 2、安装服务
+ 安装mysql
