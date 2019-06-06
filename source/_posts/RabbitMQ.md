---
title: RabbitMQ
copyright: true
date: 2019-06-05 09:27:13
tags: 消息队列
---

# RabbitMQ相关概念

#### 生产者和消费者
- producer：消息的生产者
- consumer：消息的消费者

#### Queue
- 消息队列：提供了FIFO的处理机制，具有缓存数据的能力
- 在RabbitMQ中，队列消息可以设置为持久化、临时或自动删除
- - 持久化队列：队列中的消息会在Server本地磁盘存储一份，防止系统挂掉，导致数据丢失。
- - 临时队列：队列中的数据在系统重启后就会丢失。
- - 自动删除的队列：当不存在用户连接到Server，队列中的数据就会被自动删除。

#### ExChange
类似于交换机，提供消息路由策略。在RabbitMQ中，Producer不是通过信道直接将消息发送给Queue的，而是先发给ExChange，一个ExChange与多个Queue绑定，Producer在传递消息的时候，会传递一个ROUTING_KEY,ExChange会根据这个值按照特定的路由算法，将消息分配给指定的Queue，与Queue一样，ExChange也有持久、临时和自动删除的。

#### Binding
所谓绑定就是一个特定的Exchange与一个特定的Queue绑定起来。ExChange和Queue的绑定可以是多对多的关系。

#### Virtual

# RabbitMQ的使用过程
- 客户端连接到消息队列服务器，打开一个Channel
- 客户端声明一个ExChange，并设置相关属性
- 客户端声明一个Queue，并设置相关属性
- 客户端使用Routing Key，在ExChange与Queue之间建立好绑定关系
- 客户端投递消息到ExChange
- ExChange接收到消息之后，就根据消息的key和已经绑定好的binging，进行消息路由，将消息投递到一个或多个队列里

# docker-compose.yml
```shell
version: '3.1'
services:
    rabbitmq:
        restart: always
        image: rabbitmq:management
        container_name: rabbitmq
        ports:
                - 5672:5672
                - 15672:15672
        environment:
                RABBITMQ_DEFAULT_USER: rabbit
                RABBITMQ_DEFAULT_PASS: 123456
        volumes:
                - ./data:/var/lib/rabbitmq

```

# 消息提供者
```yml
spring:
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: rabbit
    password: 123456
```
```java

@Configuration
public class RabbitConfiguration {
    @Bean
    public Queue queue() {
        return new Queue("helloRabbit");
    }
}

@Component
public class RabbitProvider {

    @Autowired
    private AmqpTemplate amqpTemplate;

    public void send() {
        String content = "Hello World " + new Date();
        System.out.println("Provider: " + content);
        amqpTemplate.convertAndSend("helloRabbit", content);
    }
}
```

# 消息消费者
```java
@Component
@RabbitListener(queues = "helloRabbit")
public class RabbitConsumer {

    @RabbitHandler
    public void process(String content) {
        System.out.println("Consumer:" + content);
    }
}
```

# 问题
- 如何保证消息队列中的数据百分之一百被消费掉
