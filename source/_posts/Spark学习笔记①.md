---
title: Spark学习笔记①
copyright: true
date: 2019-09-24 19:41:23
tags: Spark
---

# Spark

## Spark 工作原理

### 分布式
**客户端Client** 在本地编写Spark 程序，然后在本地将Spark 程序提交到Spark 集群中运行，Spark 从HDFS 中读出来的数据，会分布式存放在不同的Spark 节点上分布式处理。处理后的数据可能会被移动到别的Spark 节点中进行二次处理

所欲计算操作，都是针对多个计算节点上的数据，进行并行计算的

### 迭代式计算
Spark 计算模型可以分为n 个阶段（MapReduce 只有两个阶段：map、reduce）


# RDD（弹性分布式数据集）

- 分布式：RDD 有多个分区，多个分区散落在不同的Spark 节点上

- 弹性：RDD 每个分区在Spark 存储时，默认都是在内存中的，如果内存放不下，会放部分数据到磁盘中，这些对用户都是透明的

- 容错性：当Spark 发现RDD 的某个分区的数据丢失后，会重新获取数据，重新计算
