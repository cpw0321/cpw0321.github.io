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



