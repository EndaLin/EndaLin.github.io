---
title: MySQL技术内幕与存储引擎①
copyright: true
date: 2019-07-24 11:20:55
tags: MySQL
---

# 基本概念

**数据库**： 文件的集合， 是依照某种数据模型组织起来并存放于二级存储器中的数据集合

**数据库实例**：程序， 位于用户与操作系统之间的一层数据管理软件， 用户对数据库的任何操作， 包括数据库定义、数据查询、数据维护、数据库运行控制等都是在数据库实例下运行的， 应用程序只有通过数据库实例才能和数据库打交道


![image.png](https://upload-images.jianshu.io/upload_images/13918038-eaf94c0cd303d3f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 连接MySQL

## TCP/IP

```bash
mysql -h127.0.0.1 -u root -p
```

# InnoDB 体系架构

![image.png](https://upload-images.jianshu.io/upload_images/13918038-fcfead69c2fc40a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

后台线程的主要作用是负责刷新内存池的数据，保证缓冲池中的内存缓存的是最近的数据。此外将已修改的数据文件刷新到磁盘文件，同时保证在数据库发生异常的情况下，InnoDB 能恢复到正常运行状态。

## 后台线程

### Master Thread
Master Thread 是一个非常核心的后台线程，主要负责将缓冲池中的数据异步刷新到磁盘，保证数据的一致性

### IO Thread
在InnoDB 存储引擎中大量使用AIO（Async IO） 来处理读写IO 请求，这样可以极大提高数据库的性能

### Purge Thread
事务被提交后，其所使用的undolog 可能不再需要，因此需要PurgeThread 来回收已经使用并分配的undo 页。

### Page Cleaner Thread
将之前版本中脏页的刷新操作都放入到单独的线程中

## 内存

### 缓冲池
InnoDB 存储引擎是基于磁盘存储的，并将其中的记录按照页的方式进行管理，在数据库系统中，由于CPU 速度与磁盘速度之间的鸿沟，基于磁盘的数据库系统通常使用缓冲池技术来提高数据库的整体性能。

在数据库中进行**读取页**的操作，首先将从磁盘读到的页放在缓冲池中，这个过程称为将页“FIX” 在缓冲池，下一次再读相同的页时，该页在缓冲池中被命中，直接读取该页，否则读取磁盘上的页。

在数据库中**修改页**的操作，则首先修改缓冲池的页，然后再以一定频率刷新到磁盘上。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-bc7ca51f7794f201.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 文件

- 参数文件：配置参数，用来寻找数据库各种文件所在位置以及指定某些初始化参数，，my.cnf

- - 什么是参数：可以把数据库参数看成一个键值对

- - 参数类型：动态参数和静态参数

- 日志文件：记录MySQL 实例对某种条件作出响应时写入的文件，如错误日志、查询日志等等
- - 错误日志：对MySQL 的启动、运行、关闭过程进行了记录
- - 慢查询日志：将运行时间超过指定阀值的所有SQL 语句都记录到慢查询日志文件中，默认是10秒
- - 查询日志：记录所有对MySQL 数据库请求的信息，无论这些请求是否得到了执行
- - 二进制文件：记录了对MySQL 数据库执行更改的所有操作
- socket 文件：使用UNIX 域套接字方式进行连接时需要的文件
- pid 文件：MySQL 实例的进程ID 文件
- MySQL 表结构文件：用来存放MySQL 表结构定义文件
- 存储引擎文件

## 表

### 索引组织表

在InnoDB 存储引擎中，表都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表。

在InnoDB 存储引擎表中，每张表都有一个主键（Primary key），如果在创建表时没有显式地定义主键，那么InnoDB 存储引擎会按如下方式选择或者创建主键：
- 首先判断表中是否有非空的唯一索引，如果有，则该列为主键（选择第一个被定义的非空唯一索引）
- 如果不符合上述条件，InnoDB 存储引擎将选择表时第一个定义的为空唯一索引为主键。

### InnoDB 逻辑存储结构

![image.png](https://upload-images.jianshu.io/upload_images/13918038-e72aea911f83c4a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**表空间：**表空间可以看做是InnoDB 存储引擎逻辑结构的最高层，所有的数据都存放在表空间中。

**段：**表空间是由各个段组成的，常见的段有数据段、索引段、回滚段等等。

InnoDB 存储引擎表是索引组织的，因此数据即索引，那么数据段即为**B+树的叶子节点（Leaf node segment）**

索引段即为**B+树的非索引节点（Non-leaf node segment）**

**区**是由连续页组成的空间，在任何情况下，每个区的大小都为1 MB，在默认情况下，InnoDB 存储引擎页的大小为16KB，即一个区中一共有16 个连续的页。

**页**是InnoDB 磁盘管理的最小单位，在InnoDB 存储引擎中，默认每个页的大小为16KB

在InnoDB 存储引擎中，常见的页类型有：
- 数据页
- undo 页
- 系统页
- 事务数据页
- 插入缓冲位图页
- 插入缓冲空闲列表页
- 未压缩的二进制大对象页
- 压缩的二进制大对象页

**行**：InnoDB 存储引擎，数据是按行进行存放的

### InnoDB 行记录格式

InnoDB 存储引擎提供了Compact 和Redundant 两种格式来存放行记录数据

#### Compact 行记录格式
代办

## 索引与算法

InnoDB 存储引擎支持
- B+ 树索引
- 全文索引
- 哈希索引

B+ 树索引并不能找到一个给定键值的具体行，B+树索引能找到的只是被查找数据行的所在的页，然数据库通过把页读到内存，再从内存中进行查找，最后得到要查找的数据。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-5c0053b9eedc45f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 聚集索引
聚集索引是按照每张表的主键构造一课B+ 树，同时叶子节点存放的就是整张表的行记录数据，也将聚集索引的叶子节点成为数据页

聚集索引的存储并不是物理上连续，而是逻辑上连续

### 非聚集索引
