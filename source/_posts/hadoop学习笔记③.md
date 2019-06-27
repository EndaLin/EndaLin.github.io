---
title: Hadoop学习笔记③
copyright: true
date: 2019-06-27 12:53:00
tags: Hadoop
---

# 分布式数据库HBase
HBase 是BigTable的开源实现， 高可靠、高性能、面向列、可伸缩的分布式数据库， 主要用来存储非结构化数据和半结构化的松散数据， HBase支持超大规模数据存储， 可以通过水平扩展的方式， 利用廉价计算机集群处理超过10亿行数据和数百位列组成的数据表。

#### BigTable
BigTable是一个分布式存储系统， 利用谷歌提出的MapReduce分布式并行计算模型来处理海量初级， 利用GFS作为底层数据存储， 并采用Chubby提供协同服务管理， 可以扩展到PB级别的数据和上千台机器。

<!--more-->

#### HBase

- 利用Hadoop MapReduce来处理HBase中的海量数据， 实现高性能计算
- 利用Zookpper来作为协同服务， 实现稳定服务和失败恢复
- 利用HDFS作为高可靠的底层存储， 利用廉价集群提供海量数据存储能力

为了方便在HBase上进行数据处理， Sqoop为HBase提供了高效、便捷的RDBMS数据导入功能， Pig和Hive为HBase提供了高层语言支持。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-5c7205b6274a54a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### HBase 数据类型

HBase 是一个稀疏、多维度、排序的映射表， 这张表的索引是行健、列族、列限定符和时间戳， 每个值是一个未经解释的字符串byte[]， 没有数据类型。

用户在表中存储数据， 每一行都有一个可排序的**行键**和任意多的列。

表在水平方向有一个或者多个**列族**组成， 一个列族可以包含任意多个列， 同一个列族里面的数据存储在一起。 列族支持动态扩展， 无需预定列的数量以及类型， 所有列均以字符串形式存储， 同一张表里面的每一行数据都可以有截然不同的列。

在HBase 中执行更新操作时， 并不会删除数据旧的版本， 而是生成一个新的版本， 旧的版本仍然保留， HBase 可以对允许保留的版本的数量进行设置。

#### HBase 数据模型的相关概念
- 表
- 行
- 列族
- 列限定符
- 单元格
- 时间戳

HBase 使用坐标来定位表中数据， 四维坐标[行键， 列族， 列限定符， 时间戳]。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-05fd05458b2736a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 行式数据库和列式数据库

行式数据库主要适合于小批量的数据处理

列式数据库适合于批量数据处理和即席查询， 可以降低I/O开销， 支持大量并发用户查询， 其数据处理速度比传统方法快100倍， 因为仅仅需要处理可以回答这些查询的列， 而不是分类整理与特定查询无关的数据行， 具有较高的数据压缩比。


#### HBase 的实现原理

**HBase 的功能组件**：
- 库函数（链接到每个客户端）
- 一个Master主服务器： 负责管理和维护HBase 表的分区信息。
- 许多个Region服务器: 负责维护存储和维护分配给自己的Region， 处理来自客户端的读写请求。


客户端并不是直接从Master主服务器读取数据， 而是在获取Region 的存储位置信息后， 直接从Region上读取数据， HBase 客户端并不依赖Master 而是借助 Zookeeper 来获取Region的位置信息的。


**表与Region**

对于每一个HBase 表而言， 表中的行是根据行键的值的字段序进行维护的， 由于表中包含的行的数量可能非常庞大， 无法存储在一台机器上， 所有需要分布式存储， 需要根据行键的值对表中的行进行分区， 每个行区间构成一个Region， 它是负载均衡和数据分发的基本单位， 这些Region会被分发到不同的Region服务器。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-3f133e270f84c602.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Region 的定位**

每个Region 都有一个Region ID（表名 + 开始主键 + Region ID）来标识它的唯一性。

Region 映射表（元数据表 or .META表）： Region 标识符 + Region服务器标识符。

-ROOT-表（根数据表）， 当.META表的条目非常多时， 也会被分裂成多个Rigion， 其映射表是根数据表-ROOT-表。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-747bd59b17a162f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### HBase 运行机制
**访问流程**： 首先访问Zookeeper。 获取-ROOT-表的位置信息， 然后访问-ROOT-表， 获取.META表的信息， 接着访问.META表， 找到所需的Region具体位于哪个Region服务器， 最后找到该Region服务器读取数据。（客户端会缓存位置信息， 提高寻址效率）

Zoopkeeper 中保存了-ROOT-表的地址和Master 的地址， 客户端可以通过访问ZoopKeeper 获得-ROOT-表的地址， 最后通过三级寻址来找到所需要的地址。

HBase自身不具备数据复制和维护数据副本的功能， 而HDFS可以为HBase提供这些支持。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-5738bc978d24e27f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Region 服务器的工作原理

Region 服务器内部管理一系列的**Region 对象**和一个**HLog 文件**。

**HLog 文件**： 磁盘上面的记录文件， 它记录着所有的更新操作。

**Region 对象**： 由多个Store 组成， 每个Store 对应表中的一个列族的存储

**Store**： 包含一个MemStore 和若干个StoreFile， 其中， MemStore 是内存中的缓存， 保存最近更新的数据， StoreFile 是磁盘中的文件（B+树结构）， StoreFile 在底层的实现方式是HDFS 文件系统的HFile， 而HFile 的数据块通过采用压缩的方式存储， 压缩之后可以大大减少网络IO和磁盘IO。
![image.png](https://upload-images.jianshu.io/upload_images/13918038-aa15aaa24d9eebdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 用户读写数据的过程
当用户写入数据时， 会被分配到相应的Region 服务器执行操作， 用户数据被写入MemStore 和HLog 中， 当操作写入Hlog 之后， commit()调用才会将其返回给客户端。

当用户读取数据时， Region 服务器首先会访问MemStore 缓存， 如果数据不在缓存中， 才会到磁盘上面的StoreFile 中去寻找。

2. 缓存的刷新
MemStore的缓存容量有限， 所有会周期性地将MemStore 的数据存入到磁盘的StoreFile 中， 同时在HLog中写入一个标记， 以表示缓存中的内容已经写入到磁盘中， 每次刷新操作都会在磁盘中生成一个新的StoreFile， 所有每个Store 都会含有多个StoreFile。

Region 每次启动都会去检查HLog， 以确保缓存中的数据已经写入到StoreFile 中， 若没有， 则先将更新写入MemStore， 再刷新缓存， 写入到StoreFile中， 最后删除旧的HLog。

3. StoreFile 的合并
当StoreFile文化的数量达到一个阀值时， 会进行合并操作。

#### Store 的工作原理
![image.png](https://upload-images.jianshu.io/upload_images/13918038-cefa8a1b14a11571.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### HLog 的工作原理
每个Region 服务器维护共同一个HLog, 当Region服务器出现故障时， Zoopkeeper 会同时Master， 首先处理故障服务器上遗留的HLog 文件， 根据Region对象拆分日志， 分配到各自对象的目录下， 然后再将失效的Region分配到可用的Region服务器中， 新的服务器会将分配到给自己的HLog 日志重新执行一边， 然后刷新缓存。
