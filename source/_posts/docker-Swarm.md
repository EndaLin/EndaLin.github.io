---
title: docker-Swarm
copyright: true
date: 2019-08-03 10:53:35
tags: docker
---

# Docker Swarm

## 基本概念

Swarm 是Docker 引擎内置原生的集群管理和编排工具

### 节点
- 节点分为**管理节点** 和**工作节点**

- **管理节点**： 用于Swarm 集群的管理， 一个集群中可以拥有多个管理节点， 但是只有一个管理节点可以成为leader， leader 通过raft 协议实现

- **工作节点**： 工作节点是任务执行节点，管理节点将服务下发到工作节点执行，管理节点也默认作为工作节点， 可以通过配置让服务只运行在管理节点

运行Docker 的主机可以主动初始化一个Swarm 集群或者加入一个已存在的Swarm 集群

![img](https://docs.docker.com/engine/swarm/images/swarm-diagram.png)

<!--more-->

### 服务和任务
- **任务**： Swarm 最小的调度单位，这里可以理解为一个单一的容器
- **服务**： Services 是一组任务的集合， 服务模式有两种
> - **replicated services** ： 按照一定规则在各个工作节点上运行指定个数的任务
> - **grobal services** : 每个节点上运行指定个数的任务

![](https://docs.docker.com/engine/swarm/images/services-diagram.png)


## Swarm 的创建与使用

### 创建集群

**管理节点**

```
$ docker-machine create -d virtualbox manager

$ docker-machine ssh manager

docker@manager:~$ docker swarm init --advertise-addr 192.168.99.100
Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

**工作节点**
```
$ docker-machine create -d virtualbox worker1

$ docker-machine ssh worker1

docker@worker1:~$ docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377

This node joined a swarm as a worker.
```

**部署服务**

在管理节点运行如下命令， 该服务默认在管理节点中也启动， 启动两个节点，一个在管理节点，一个在工作节点
```
docker service create --replicas 2 -p 80:80 --name nginx nginx:1.13.7-alpine
```


**查看集群**

在leader 中运行如下命令
```bash
docker node ls
```


### 使用docker-compose 创建集群

**docker service create**： 每次只能启动一个服务

**docker-compose**： 一次可以启动多个服务

此处需要注意的是：需要在**管理节点中运行docker-compose.yml** 文件
