---
title: Spark学习笔记②
copyright: true
date: 2019-09-26 14:01:01
tags: Spark
---

# Spark SQL

Spark SQL 是Spark中的一个模块，主要用于结构化数据的处理，同时还可以作为分布式的SQL 查询引擎。

DataFrame 是以列的形式组织的，分布式的数据集合，可以通过很多源来构建，包括结构化的数据文件、Hive 中的表、外部的关系型数据库以及RDD



#Spark Streaming

## StreamingContext 详解

一个StreamingContext 定义之后，必须做一下几件事：
- 通过创建输入DStream 来创建输入数据源
- 通过对DStream 定义transformation 和output算子操作，来定义实时计算逻辑
- 调用StreamingContext 的start() 方法，来等待应用程序的终止（可以使用CTRL+C手动停止，或者让它持续不断地运行计算）
- 调用StreamingContext 的stop() 方法，来停止应用程序

需要注意几点：
- 只要一个StreamingContext 启动之后，就不能再往其中添加任何计算逻辑了，例如在start() 方法之后，再给某个DStream 执行一个算子
- 一个StreamingContext 不能重启，在stop 之后不能再Start
- 一个JVM 同时只能有一个StreamContext启动
- 调用stop 方法时，会同时停止内部的SparkContext，如果希望继续用SparkContext 创建其它类型的Context，则使用stop(false)
