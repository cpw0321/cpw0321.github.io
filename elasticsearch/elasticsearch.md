# es基础知识

## 一、安装

Docker安装ES、Kibana、head、IK

参考：
https://www.cnblogs.com/qq1445496485/p/16472862.html   
1、拉取es镜像
```shell
# 拉取镜像
docker pull elasticsearch:7.4.2

# 创建文件
mkdir -p /elasticsearch/data
mkdir -p /elasticsearch/logs
mkdir -p /elasticsearch/config
mkdir -p /elasticsearch/plugins
```
2、修改配置文件  
vi /elasticsearch/config/elasticsearch.yml 
```text
# 修改集群名称
cluster.name: "docker-cluster"
# 修改网络请求host,0.0.0.0可以被所有外网访问到
network.host: 0.0.0.0
http.port: 9200
# 开启es跨域
http.cors.enabled: true
http.cors.allow-origin: "*"
# 开启安全控制（即登录es和kibana后需要账号密码）
# xpack.security.enabled: true
# xpack.security.transport.ssl.enabled: true
# http.cors.allow-headers: Authorization,Content-Type,X-Requested-with,Content-Length
```
3、启动
```shell
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
-e TZ="Asia/Shanghai" \
-e LANG="en_US.UTF-8" \
-e ELASTIC_PASSWORD="123456" \
-v /Users/zhigui/code/docker/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /Users/zhigui/code/docker/elasticsearch/data:/usr/share/elasticsearch/data \
-v /Users/zhigui/code/docker/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.12.0
```
4、安装head
```shell
docker pull mobz/elasticsearch-head:5
docker run --name es-head -p 9100:9100 --restart=always -d mobz/elasticsearch-head:5
```
+ 问题：  
4.1、无法新建索引报406错误，协议不支持  

```text
vi _site/vendor.js

6886行 contentType: "application/x-www-form-urlencoded
　　　　改成
　　　　contentType: "application/json;charset=UTF-8"
7573行 var inspectData = s.contentType === "application/x-www-form-urlencoded" &&
　　　　改成
　　　　var inspectData = s.contentType === "application/json;charset=UTF-8"
```

5、安装ik分词器
```text
#下载地址
https://github.com/medcl/elasticsearch-analysis-ik/tags
```
解压到plugins目录下，重启es

6、安装kibana
```shell
# 拉取镜像
docker pull kibana:7.4.2

# 创建文件夹
mkdir -p /elasticsearch/kibana/config
cd /elasticsearch/kibana/config
# 创建文件
vi kibana.yml
```
kibana.yml配置文件
```shell
#查看ES内部地址 下面安装，运行kibana要用到
docker inspect elasticsearch |grep IPAddress
```
```text
server.name: kibana
server.host: "0.0.0.0"
elasticsearch.hosts: [ "http://172.17.0.2:9200" ] # http://172.17.0.2:9200 TODO 修改为自己的ES内部地址
xpack.monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.username: "elastic"  # es账号
elasticsearch.password: "123456"   # es密码
i18n.locale: zh-CN # 中文
```
运行
```shell
# 没有编写kibana.yml文件的启动方式
docker run --name kibana --restart=always -e ELASTICSEARCH_HOSTS="http://172.17.0.2:9200" -p 5601:5601 -d kibana:7.12.0
 
# 编写了kibana.yml文件的启动方式
docker run --name kibana --restart=always \
-v /elasticsearch/kibana/config/kibana.yml:/usr/share/kibana/config/kibana.yml \
-p 5601:5601 -d kibana:7.12.0
```
汉化
```text
/config/kibana.yml，添加
i18n.locale: "zh-CN"
```

## 二、基础知识

使用场景：全面检索
+ 百度搜索
+ github搜索  
...

### 1、概念

参考：https://www.kuangstudy.com/bbs/1354069127022583809  
es--->mysql

* 索引--->数据库  
* 类型--->表，6.x在使用, 7.x淡化，8.x废弃  
* 类型--->数据库类型，varchar  
* 文档--->行
* 倒序索引

### 2、类型

![img.png](images/img.png)

### 3、操作

增删查改

+ put 修改， 更推荐使用 post _update区别，只更新某个字段， put某个字段不写会为空
+ get 查询  /test/user/_search?q=name:张三  

### 4、条件查询

+ match匹配---> ==
+ source过滤字段---> select name
+ sort排序---> order by
+ form、size分页---> limit offset
+ must---> and
+ should---> or
+ must_not---> not
+ filter过滤

```text
keyword类型的不会被分词器解析
```

## 三、插件
* head 图形化界面(Kibana更好用，elk)
* ik分词器
    + ik_smart 最少切分
    + ik_max_word 最小颗粒度划分
    + ik分词器增加自己的字典,即自己的单词  
        在plugins-ik-config-*.dc
