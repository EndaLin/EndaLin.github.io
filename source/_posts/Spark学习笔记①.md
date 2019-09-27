---
title: Spark学习笔记①
copyright: true
date: 2019-09-24 19:41:23
tags: Spark
---

# Spark

## Spark 计算模式

Spark 支持两种RDD 操作：transformation 和action。transformation 操作会针对已有的RDD 创建一个新的RDD，而action 主要是对RDD 进行最后的操作，例如遍历、reduce、保存到文件等等，并可以将结果返回给Driver 程序。

transformation 具有lazy 特性，一个程序只有transformation 是不会执行的，只有触发了action 操作，才会触发所有的transformation 操作。

## Spark 工作原理

### 分布式
**客户端Client** 在本地编写Spark 程序，然后在本地将Spark 程序提交到Spark 集群中运行，Spark 从HDFS 中读出来的数据，会分布式存放在不同的Spark 节点上分布式处理。处理后的数据可能会被移动到别的Spark 节点中进行二次处理

所欲计算操作，都是针对多个计算节点上的数据，进行并行计算的

### 迭代式计算
Spark 计算模型可以分为n 个阶段（MapReduce 只有两个阶段：map、reduce）


## RDD（弹性分布式数据集）

- 分布式：RDD 有多个分区，多个分区散落在不同的Spark 节点上

- 弹性：RDD 每个分区在Spark 存储时，默认都是在内存中的，如果内存放不下，会放部分数据到磁盘中，这些对用户都是透明的

- 容错性：当Spark 发现RDD 的某个分区的数据丢失后，会重新获取数据，重新计算

<!-- more -->

## 并行化创建RDD

如果需要通过并行化创建RDD，需要针对程序中的集合，调用SparkContext 的parallelize 方法，将集合中的数据拷贝到集群上，形成一个分布式数据集合（RDD），相当于，集合中的一部分数据回到一个节点上，另一部分数据就到另一个节点上，然后使用并行的方式来操作这个分布式数据集合

调用parallelize 方法是有一个重要的参数是设置partition 的数量，Spark 默认会根据集群的情况来设置partition 的数量

## 使用本地文件和HDFS 创建RDD

调用SparkContext 的textFile() 方法，可以针对本地文件或者HDFS 文件来创建RDD

注意：
- 如果是在Spark 集群上针对本地文件，那么需要将文件拷贝到所有Worker 节点上
- Spark 的textFile 方法支持针对目录、压缩文件以及通配符来进行RDD 创建
- Spark 默认为HDFS 文件的每一个block 创建一个partition


## Spark 常用算子
[Spark 常用算子](https://blog.csdn.net/dream0352/article/details/62229977)
