# mysql

## 一、安装

### 1.1. docker运行

```text
docker run -d  --name mysql \
-v ~/code/docker/mysql/data:/var/lib/mysql \
-v ~/code/docker/mysql/conf:/etc/mysql \
-v ~/code/docker/mysql/log:/var/log/mysql \
-p 3306:3306 \
-e TZ=Asia/Shanghai \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7
```