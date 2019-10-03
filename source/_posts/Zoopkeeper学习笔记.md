---
title: Zoopkeeper学习笔记
copyright: true
date: 2019-10-02 20:31:28
tags: Zoopkeeper
---

# Zoopkeeper 工作机制

Zoopkeeper 是一个给予观察者模式设计的分布式服务管理框架，它负责存储和管理大家都关心的数据，接受观察者的注册，一旦数据状态发生了变化，Zoopkeeper 就负责通知已经在Zoopkeeper 上注册的那些观察者做出相应的反应。

Zoopeeker = 文件系统 + 通知机制

# Zoopkeeper 特点
- Zoopkeeper集群，一个leader, 多个跟随着组成集群
- 只要集群中半数以上节点存货，Zoopeekper 集群就能存活
- 全局数据的一致性，集群中每一个节点的数据都是一致的
- 集群对更新请求是顺序执行的，来自同一个client 的更新请求是按其发送顺序来顺序执行的
- 数据更新的原子性：一次数据要么更新成功，要么更新失败
- 实时性：在一定时间内，Client 总能读取到最新的数据

<!--more-->

# Zoopkeeper 应用场景

## 统一命名服务
在分布式环境下，需要对服务或者命名进行统一命名操作，同一个命名下，会有多个不同IP 的服务器统一对外提供相同的服务。

## 统一配置服务
- 在分布式环境下，统一集群中的配置文件，如kafka 集群
- 对配置文件修改后，能够迅速同步到各个节点上，此处，配置管理可以交给Zookeeper 上的一个Znode，各个客户端服务器监听这个Znode，一旦Znode 的数据发生了变化，Zookeeper 会通知所有节点

## 统一集群管理
- 在分布式环境中，Zoopkeeper 可以实时监控每一个节点的状态变化，节点信息可以存储在Zoopkeeper 中的一个ZNode 中
- 客户端能实时洞察到服务器上下线的状态变化

## 软负载均衡
在Zoopkeeper 中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求

# Zoopeekeer 的安装
```yml
version: '3.1'

services:
  zoo1:
    image: zookeeper
    restart: always
    hostname: zoo1
    ports:
      - 2181:2181
    volumes:
      - ./zoo1/data:/data
      - ./zoo1/datalog:/datalog
      - ./zoo1/conf://apache-zookeeper-3.5.5-bin/conf
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo2:
    image: zookeeper
    restart: always
    hostname: zoo2
    ports:
      - 2182:2181
    volumes:
      - ./zoo2/data:/data
      - ./zoo2/datalog:/datalog
      - ./zoo2/conf://apache-zookeeper-3.5.5-bin/conf
    environment:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=0.0.0.0:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo3:
    image: zookeeper
    restart: always
    hostname: zoo3
    ports:
      - 2183:2181
    volumes:
      - ./zoo3/data:/data
      - ./zoo3/datalog:/datalog
      - ./zoo3/conf://apache-zookeeper-3.5.5-bin/conf
    environment:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=0.0.0.0:2888:3888;2181

```

配置传输解读
- ZOO_MY_ID：表示第几号服务器
- server.A=B:C:D
- - A 是一个数字，表示这个是第几号服务器
- - B 是这个服务器的IP 地址
- - C 是这个服务器与集群中的Leader 服务器交换信息的端口
- - D 是万一集群中的Leader 服务器挂了，需要一个端口来重新进行选举，选举出一个新的Leader，这个端口是就是在选举时用来通信的端口

启动

```bash
COMPOSE_PROJECT_NAME=zk_test docker-compose up
```

## docker 作为客户端连接

```bash
docker run -it --rm \
>         --link zoo1:zk1 \
>         --link zoo2:zk2 \
>         --link zoo3:zk3 \
>         --net zktest_default \
>         --name zkClient \
>         zookeeper
```

进入容器执行
```bash
zkCli.sh -server zk1:2181,zk2:2181,zk3:2181
```

参考：
- [docker - zookeeeper 集群部署](https://hub.docker.com/_/zookeeper)

# Zookeeper 内部原理

## 选举机制paxos
- 半数机制：集群中半数以上的机器存活，集群可用，所有Zookeeper 适合安装奇数台服务器
- Zookeeper 选举机制：在配置文件中，没有指定master 和Slave，但是Zookeeper 工作时，使用一个节点是Leader（通过内部选举机制临时产生的）

## 节点类型
- 持久型：客户端和服务器端断开连接后，创建的节点不删除
- 短暂型：客户端和服务器端断开连接后，创建的节点自己删除


- 持久化目录节点
- 持久化顺序编号目录节点
- 临时目录节点
- 临时顺序编号目录节点


/apache-zookeeper-3.5.5-bin/conf

# Zookeeper 监听器原理

监听器原理详解
- 首先要有一个main() 线程
- 在main 线程中创建Zookeeper 客户端，这是就会创建两个线程，一个负责网络连接通信（connect），另一个负责监听（listener）
- 通过connect 线程将注册的监听时间发送给Zookeeper
- 在Zookeeper 的注册监听器列表中将注册的监听时间添加到列表中
- Zookeeper 监听到有数据或者路径变化，就会将这个消息发送到listener 线程
- listener 线程内部调用了process（） 方法

常见的监听
- 监听节点数据的变化
- 监听子节点增减的变化

# Zookeeper 写数据流程
- Client 向Zookeeper 的Server1 写数据，发出一个写请求
- 如果Server1 不是Leader，那么Server1 会把接收到的请求进一步转发给Leader，因为Zookeeper 的Server 里面有一个是Leader，这个Leader 会将写请求广播给各个Server，各个Server写成功之后就会通知Leader
- 当Leader 收到半数以上的Server 写成功之后，那就说明数据写成功了，写成功之后，Leader 会告诉Server1 写成功了
- Server1 就会进一步通知Client 数据写成功了
