# rabbitmq

## 一、安装

### 1.1. docker运行
```text
docker run -d -p 5672:5672 -p 15672:15672 --name rabbitmq rabbitmq:management
```

## 场景
### 1.1死信队列
参考：https://learnku.com/articles/64886  
通俗来讲，无法被正常消费的消息，我们可以称之为死信。我们将其放入死信队列，单独处理这部分 “异常” 消息。
当消息符合以下的一个条件时，将会称为死信。

+ 消息被拒绝，不重新放回队列（使用 basic.reject/basic.nack 方法拒绝消息，并且这两个方法的参数 requeue = false）
+ 消息 TTL 过期
+ 队列达到最大长度


### 1.2 问题
#### 1.2.1 channel复用问题  
问题描述：  
[ERROR] code: 505, UNEXPECTED_FRAME   

原因：  
channel被不同线程复用导致安全问题，比如消费者和生产者复用，生产者之间复用。而消费者之间是不会的。  


### 1.3 延迟队列

RabbitMQ延迟队列可以用于在一定时间后将消息重新投递到队列中，以实现延迟消息的发送。通过使用延迟队列，您可以实现以下几个功能：
+ 延迟任务执行：通过将任务放入延迟队列中，可以实现在指定的时间后再次将消息发送到目标队列，从而实现延迟任务执行的效果。
+ 重试机制：当某个任务失败时，我们可以将任务放入延迟队列中，并设置一定的重试时间，以实现自动重试的效果。
+ 流量控制：通过将消息放入延迟队列中，可以控制消息的流量，以避免过多的消息同时进入目标队列，从而导致系统崩溃或者运行缓慢等问题。
+ 订单超时处理：在电商网站等场景下，订单支付后需要在一定时间内完成处理，如果在规定时间内未完成，需要自动关闭订单。通过将订单信息放入延迟队列中，可以实现订单超时处理的效果。

总之，RabbitMQ延迟队列可以帮助我们更好地实现消息的延迟发送、重试机制、流量控制和订单超时处理等功能，从而提高系统的可靠性和稳定性。  


#### 1.3.1、下载插件
http://www.rabbitmq.com/community-plugins.html  

#### 1.3.2、docker安装rabbitmq
```text
docker run -d --name rabbit -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq:management
```

#### 1.3.3、docker安装插件
```text
安装插件
docker cp rabbitmq_delayed_message_exchange-3.9.0.ez rabbitmq:/plugins

进入容器
docker exec -it rabbit /bin/bash

开启插件
rabbitmq-plugins enable rabbitmq_delayed_message_exchange

查询插件
rabbitmq-plugins list

重启使插件生效
docker restart rabbit
```
