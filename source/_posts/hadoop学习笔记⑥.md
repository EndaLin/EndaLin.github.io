---
title: hadoop学习笔记⑥
copyright: true
date: 2019-07-13 10:30:34
tags: hadoop
---

# Shuffle and combiner

![image.png](https://upload-images.jianshu.io/upload_images/13918038-fa94a71367b9778c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

# HDFS 2.0

HDFS 1.0 只有一个名称节点， 存在单点故障问题。

HDFS 2.0， 采用HA 架构。 在一个典型的HA 集群中， 一般设置两个名称节点， 一个名称节点处于活跃状态， 另一个处于待命状态。 处于活跃状态的名称节点负责对外处理所有客户端的请求， 而处于待命状态的节点则作为备用节点。

待命的名称节点是活跃名称节点的“热备份”， 活跃名称节点的状态信息必须实时同步到待命节点， 两个名称节点的状态同步， 可以借助一个共享存储系统来实现（Zookeeper）， 活跃名称节点将更新数据写入到共享存储系统， 待命名称节点会一直监听该系统， 一旦有新的写入， 就立即从公共存储系统中读取这些数据并加载到自己的内存中， 从而保证与活跃名称节点状态的完全同步。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-a2871b1dba15078f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
