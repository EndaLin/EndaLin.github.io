---
title: kafka
copyright: true
date: 2019-10-08 17:21:03
tags: kafka
---

# kafka

kafka 是一个分布式消息队列，kafka 对消息保存是根据Topic 进行归类，发送者称为Producer，消息接受者称为Consumer，kafka 集群有多个kafka 实例组成，每个实例称为 broker

无论是kafka 集群还是consumer 都依赖Zookeeper 集群保存一些meta 信息， 来保证系统的可用性


在kafka 集群中，有leader 节点和follower 节点，follower 节点是备份数据用的，客户端只有访问leader 节点时才能得到相应，这个与Zookeeper 不同

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
    volumes:
    - ./logs:/kafka
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
    - 9093:9093
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka2
      KAFKA_ADVERTISED_PORT: 9093
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
    volumes:
    - ./logs:/kafka
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
    - 9094:9094
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka3
      KAFKA_ADVERTISED_PORT: 9094
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
    volumes:
    - ./log:/kafka
    extra_hosts:
     - "zoo1:10.60.2.128"
     - "zoo2:10.60.2.128"
     - "zoo3:10.60.2.128"
```
