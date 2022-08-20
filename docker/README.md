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

## 2、安装
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

## 3、命令参数
+ -d 后台运行
+ -v 挂载卷