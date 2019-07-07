---
title: Hadoop学习笔记④
copyright: true
date: 2019-06-28 09:05:05
tags: Hadoop
---

# NoSQL

典型的NoSQL 数据库通常包括键值数据库、列族数据库、文档数据库和图数据库。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-b210cb51b9e0e2f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 键值数据库（Redis）

键值数据库会使用一个哈希表， 这个表有一个特定的Key 和一个指定特定的Value。

键值数据库可以划分为**内存键值数据库**和**持久化键值数据库**。

弱点： 条件查询、多表查询， 不支持回滚， 无法支持事务。

<!--more-->

#### 列族数据库（BigTable、HBase）

列族数据库一般采用列族数据库模型， 数据库由多个行构成， 每行数据包括多个列族， 不同的行可以具有不同数量的列族， 属于同一列族的数据会被存储在一起。

#### 文档数据库（MongoDB）

在文档数据库中， 文档是数据库的最小单位。 虽然每一种文档数据库的部署有所不同， 但是大都假定文档以某种标准化格式封装并对数据进行加密， 同时用多种格式进行解密， 包括XML、 YAML 和JSON 等等。

文档数据库既可以根据键Key来构建索引， 也可以基于文档内容来构建索引。

#### 图数据库

图数据库是以图论为基础， 一个图是一个数学概念， 用来表示一个对象集合， 包括顶点以及连接顶点的边。

# NoSQL的三大基石
NoSQL 的三大基石包括CAP、 BASE 和最终一致性。

# NewSQL 数据库（Spanner）

NewSQL 不但具有NoSQL 对海量数据的存储管理能力， 还保持了传统数据库支持ACID 和SQL 等特性。

# 各类数据库的汇集

![image.png](https://upload-images.jianshu.io/upload_images/13918038-aa8c6c84667a1545.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
