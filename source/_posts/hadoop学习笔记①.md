---
title: Hadoop学习笔记①
copyright: true
date: 2019-06-25 09:57:47
tags: Hadoop
---

# Hadoop 简介

Hadoop 是一个开源的、可运行于大规模集群上的分布式计算平台， 实现了MapReduce计算模型和分布式文件系统HDFS等功能

Hadoop的核心是分布式文件系统HDFS和MapReduce， HDFS是针对谷歌GFS的开源实现， 是面向普通硬件环境的分布式文件系统， 具有支持大规模数据的分布式存储。

MapReduce允许用户在不了解分布式系统底层细节的情况下开发并行应用程序， 整合分布式文件系统上的数据， 保证分析和处理数据的高效性。

<!--more-->

# Hadoop生态系统

![image.png](https://upload-images.jianshu.io/upload_images/13918038-b88cefc97067930a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### HDFS

Hadoop分布式文件系统是Hadoop项目的两大核心之一， 具有处理超大数据、流式处理、可以运行在廉价商用服务其上等优点, HDFS在访问应用层程序时，具有很高的吞吐率， 因此对于超大数据集的应用程序而言，选择HDFS作为底层数据存储是较好的选择。

#### HBase

HBase是一个高可靠性、高性能、可伸缩、实时读写、分布式的列式数据库， 一般采用HDFS作为底层数据存储

HBase是针对Google BigTable的开源实现， 具有强大的非结构化数据存储能力

HBase具有良好的横向扩展能力

#### MapReduce

MapReduce是一种编程模型， 用于大规模数据的并行运算， 它将复杂、运行于大规模集群上的并行计算过程高度抽象到了两个函数--Map和Reduce上

MapReduce的核心思想是分而治之， 它把输入的数据切分为若干个数据块， 分发给一个主节点管理下各个分结点来共同并行完成， 最后， 通过整合各个结点的中间结果得到最终结果。


#### Hive

Hive是基于Hadoop的一个数据存库工具， 可以用于对Hadoop文件中的数据集进行整理、特殊查询和分析存储， 提供了类似SQL语言的查询语言Hive QL， 可以通过Hive QL快速实现简单的MapReduce统计， Hive自身可以将Hive QL语句装换成MapReduce任务进行运行， 适合数据存库的统计分析。

#### Pig

Pig是一种数据流语言和运行环境， 适合使用Hadoop和MapReduce平台来查询大型半结构化数据集

#### Mahout
数据挖掘等智能应用相关

#### Zookeeper
Zookeeper是针对谷歌Chubby的一个开源实现，是高效可靠的协同工作系统， 提供分布式锁之类的基本服务， 用于构建分布式应用， 减轻分布式应用程序所承担的协调任务。

#### Flume
Flume是Cloudera提供的一个高可用、高可靠、分布式的海量日志采集、聚合和传输的系统。

#### Sqoop(SQL-to-Hadoop)

主要用于Hadoop和关系数据库之间交换数据

#### Ambari

一种基于Web的根据， 支持Hadoop集群的安装、部署、配置和管理。
