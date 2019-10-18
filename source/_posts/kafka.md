---
title: kafka
copyright: true
date: 2019-10-08 17:21:03
tags: kafka
---

# kafka

kafka 是一个分布式消息队列，kafka 对消息保存是根据Topic 进行归类，发送者称为Producer，消息接受者称为Consumer，kafka 集群有多个kafka 实例组成，每个实例称为 broker

无论是kafka 集群还是consumer 都依赖Zookeeper 集群保存一些meta 信息， 来保证系统的可用性


在kafka 集群中，有leader 节点和follower 节点，follower 节点是备份数据用的，客户端只有访问leader 节点时才能得到响应，这个与Zookeeper 不同

在Zookeeper 集群中，客户端可以访问leader 节点也可以访问follower 节点，如果客户端访问follower 节点，如果是写请求，follower节点会把写请求转发到leader 节点中，leader 节点在执行完读写请求后，会把执行结果返回给follower 节点，follower 节点再把结果返回给客户端；如果是读请求，follower 会直接读

同一组的消费者不能同时消费同一个分区的kafka

# 部署
```bash
version: '3'

services:
  kafka1:
    image: wurstmeister/kafka
    restart: always
    hostname: kafka1
    container_name: kafka1
    ports:
    - 9092:9092
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka1
      KAFKA_ADVERTISED_PORT: 9092
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
      KAFKA_LISTENERS: PLAINTEXT://kafka1:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka1:9092
    volumes:
    - ./kafka1:/kafka
    extra_hosts:
     - "zoo1:10.60.2.128"
     - "zoo2:10.60.2.128"
     - "zoo3:10.60.2.128"

  kafka2:
    image: wurstmeister/kafka
    restart: always
    hostname: kafka2
    container_name: kafka2
    ports:
    - 9093:9092
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka2
      KAFKA_ADVERTISED_PORT: 9092
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
      KAFKA_LISTENERS: PLAINTEXT://kafka2:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka2:9092
    volumes:
    - ./kafka2:/kafka
    extra_hosts:
     - "zoo1:10.60.2.128"
     - "zoo2:10.60.2.128"
     - "zoo3:10.60.2.128"

  kafka3:
    image: wurstmeister/kafka
    restart: always
    hostname: kafka3
    container_name: kafka3
    ports:
    - 9094:9092
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka3
      KAFKA_ADVERTISED_PORT: 9092
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
      KAFKA_LISTENERS: PLAINTEXT://kafka3:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka3:9092
    volumes:
    - ./kafka3:/kafka
    extra_hosts:
     - "zoo1:10.60.2.128"
     - "zoo2:10.60.2.128"
     - "zoo3:10.60.2.128"
```

# kafka 生成过程分析

## 写入方式
producer 采用push 模式将消息发布到broker 中，每条消息都被追加append 到分区patition 中

## 分区(partition)
消息发送时都会被发送到一个topic，其本质就是一个目录，而topic 是由一些partition logs（分区日志）组成

每一个分区内的消息都是有序的，生产的消息被不断追加到Partition 中，其中的每一个消息被赋予了唯一的offset 值

**分区的原因：**
- 方便在集群扩展，每个Partition 可以通过调整以适应它所在的机器，而一个Topic 又可以有多个partition 组成，因此整个集群就可以适应任意大小的数据了
- 提高并发，以partition 为单位读写

**分区原则：**
- 指定了partition， 则直接使用
- 未指定partition 但指定key，通过key 的value hash出一个partition
- partition 和key 都未指定，使用轮训选出一个partition

## 副本Replication
同一个partition 可能会有多个replication，一旦broker 挂了，其上所有partition 的数据都不可被消费，同时也不能再往这些partition 中写入数据，此时，就会从partition 中的replication 中重新选举出一个leader ，producer 和consumer 只与这个leader 交互，其它fllower 从leader 中复制数据

## Producer 写入流程
- producer 先从broker-list 获取partition 的leader
- producer 将消息发送给该leader
- leader 将消息写入本地log
- followers 从leader pull 消息，followers 写完之后想leader 发送ack

leader 写入成功之后，可以立即反馈给客户端，写操作结束，也可以等到followers 写完之后再反馈。而前者的效率是后者的十倍。

## Cuscomer

kafka 提供了两套consumer API:高级 API和低级API

**高级API**  
优点：
- 简单
- 不需要自行去管理offset，系统通过zookeeper 自行管理
- 不需要管理分区，副本等情况，系统自动管理

缺点：
- 不能控制offset，不能细化控制分区、副本等等

**低级API**

优点：
- 自主控制offset，管理分区、副本
缺点：
- 太复杂
