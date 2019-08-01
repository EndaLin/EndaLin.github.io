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

每个数据节点都要向集群中的所有名称节点注册， 并周期性地向名称节点发送“心跳”和块信息， 报告自己的状态， 同时也处理来自名称节点的指令。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-a2871b1dba15078f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# YARN（新一代资源管理调度框架）

YARN 把JobTracker（Hadoop1.0 中的资源管理调度功能） 这一组件的任务进行拆分， 拆成三个部分： 资源管理、任务调度和任务监控

YARN 包括ResourceManager（资源管理）、 ApplicationMaster（任务调度和监控）和 NodeManager（负责执行原TaskTracker（DataNode） 的任务）

![image.png](https://upload-images.jianshu.io/upload_images/13918038-763cfc7ddba81241.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# YARN 体系结构

![image.png](https://upload-images.jianshu.io/upload_images/13918038-70d99877de987efd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**ResourceManager**
- 处理客户端请求
- 启动/监控ApplicationMaster
- 监控NodeManager
- 资源分配与调度

**ApplicationMaster**
- 为应用程序申请资源， 并分配给内部任务
- 任务调度、监控与容错

**NodeManager**
- 单个节点上的资源管理
- 处理来自ResourceManager 的命令
- 处理来自ApplicationManager 的命令

# YARN 的部署

![image.png](https://upload-images.jianshu.io/upload_images/13918038-3650ebbb1b644ced.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### YARN 的工作流程

- 用户编写客户端应用程序， 向YARN 提交应用程序， 提交的内容包括ApplicationMaster程序、启动ApplicationMaster 的命令、用户程序等等。
- YARN 中的ResourceManager 负责接受和处理来自客户端的请求， 为应用程序分配一个容器， ResourceManager 的应用程序管理器会与该容器所在的NodeManager 通信， 为该应用程序所在的容器启动一个ApplicationMaster
- ApplicationMaster 被创建后会先向ResourceManager 注册， 然后用户可以通过ResourceManager 来查看程序的运行状态。

**以下是应用程序执行步骤**

- ApplicationManager 采用轮询的方式通过RPC 协议向ResourceMAnager 申请资源
- ResourceManager 以“容器” 的形式向提出申请的ApplicationMAster 分配资源， 一旦申请到资源后， 就会与该容器所在的NodeManager 进行通信， 要求它启动任务
- 当ApplicationMaster 要求容器启动任务时， 它会为任务设置好运行环境， 然后将任务启动命令写到一个脚本中， 最后通过在容器中运行该脚本来启动任务。
- 各个任务通过某个RPC 协议向ApplicationMaster 汇报自己的状态和进度， 让ApplicationMaster 可以随时掌握各个任务的运行状态， 从而可以在任务失败时重启任务
- 应用程序运行完成后， ApplicationMaster 向ResourceManager 的应用管理器注销并关闭自己。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-3eb185c39ec06dc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Spark

Spark 同时支持一下三种类型：
- 复杂的批量数据处理： 时间跨度通常在数十分钟到数小时之间
- 基于历史数据的交互式查询： 时间跨度通常在数十秒到数分钟之间
- 基于实时数据流的数据处理： 时间跨度通常在数百毫秒到数秒之间

Spark 专注于数据的处理和分析， 数据的存储还是需要借助Hadoop 分布式文件系统HDFS 来实现。

Spark 生态系统主要包括Spark Core、 Spark SQL、 Spark Streaming、 MLlib和 GraphX 等组件

- **Spark Core**： Spark Core 包含Spark 的基本功能， 如内存计算、任务调度、部署模式、故障恢复、存储管理等， 主要面向批数据处理。 Spark 建立在统一的抽象RDD 之上， 使其可以以基本一致的方式应对大数据处理场景。
- **Spark SQL**： Spark SQL 允许开发人员直接处理RDD， 同时也可以查询Hive、 HBase等外部数据源。 Spark SQL 的一个重要特点是其能够统一处理关系表和RDD， 使得开发人员不需要自己编写Spark 应用程序， 开发人员可以轻松使用SQL 命令进行查询， 并进行更复杂的数据分析。
- **Spark Streaming**： Spark Streaming 支持高吞吐量、 可容错处理的实时数据处理， 其核心思路是将流数据分解成一系列短小的批处理作业， 每个短小的批处理作业都可以使用Spark Core 进行快速处理， Spark Streaming 支持多种数据输入源， 如Kafka、 Flume和TCP 套接字等。
- **MLlib**： 机器学习
- **GraphX（图计算）**

![image.png](https://upload-images.jianshu.io/upload_images/13918038-f0ff002920cbb084.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Spark 运行框架

### 基本概念
- RDD： 弹性分布式数据集， 是分布式内存的一个抽象概念， 提供了一个高度受限的共享内存模型
- DAG： 有向无环图， 反映RDD 之间的依赖关系
- Executor： 是运行在工作节点（Worker Node）上的一个进程， 负责运行任务， 并为应用程序存储数据
- 应用Application： 用户编写的Spark 应用程序
- 任务： 运行在Executor 上的工作单元
- 作业： 一个作业包含多个RDD 及作用于相应RDD 上的各种操作
- 阶段： 是作业的基本调度单位， 一个作业分为多个阶段， 一个阶段又有多个任务task

![对基本概念的定义](https://www.cnblogs.com/shishanyuan/p/4721326.html)

### 架构设计

- 集群资源管理器Cluster Manager（资源管理器可以使用YARN）
- 运行作业任务的工作节点Work Node
- 每个应用的任务控制节点Driver
- 每个工作节点上负责具体任务的执行进程Executor

![image.png](https://upload-images.jianshu.io/upload_images/13918038-a316a77fc4998030.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当执行一个应用时， 任务控制节点会向集群管理器（Cluster Manager）申请资源， 启动Executor， 并向Executor 发送应用程序代码和文件， 然后在Executor 上执行任务， 运行结束后执行结果会返回任务控制节点， 或者写到HDFS 或其它数据库中。

### Spark 运行的基本流程
- 当一个Spark 应用被提交时， 首先需要为这个应用构建起基本的运行环境， 即由任务控制节点（Driver） 创建一个SparkContext， 由SparkContext 负责和资源管理器Cluster Manager 的通信以及进行资源的申请、任务的分配等工作， 然后SparkContext 会向资源管理器（ResourceManager or Cluster Manager）注册并申请运行Executor 的资源
- 资源管理器为Executor 分配资源， 并启动Executor 进程， Executor 运行情况将随着“心跳” 发送到资源管理器上
- SparkContext 根据RDD 的依赖关系构建DAG 图， DAG 图提交给DAG 调度器进行解析， 将DAG 图分解为多个“阶段”（每个阶段都是一个任务集）， 并且计算出各个阶段的依赖关系， 并且把一个个阶段中的任务集分发给Executor 运行， 同时SparkContext 将应用程序代码发放给Executor
- 任务在Executor 上运行， 把执行结果反馈给任务调度器， 然后反馈给DAG 调度器， 运行完毕之后写入数据并释放资源


**注意**

- 每个应用都有自己专属的Executor， 并且该进程在应用运行期间一直驻留， Executor 进程以多线程方式运行任务， 减少多进程任务频繁的启动开销， 使得任务执行变得非常高效和可靠
- Spark 运行过程与资源管理器无关， 只要能够获取Executor 进程并保持通信即可
- Executor 上有一个BlockManager 存储模块， 把内存和磁盘共同作为存储设备， 在处理迭代计算任务时， 不需要把中间结果写入到HDFS 等文件系统， 而是直接放在这个存储系统上， 后续有需要则直接读取， 在交互式查询的场景中， 也可以把表提前缓存到这个存储系统上， 提高读写IO性能

![image.png](https://upload-images.jianshu.io/upload_images/13918038-7bb8a8f5d6af2d09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

任务采用数据本地性和推测执行等优化机制
- 数据本地化：将计算移到数据所在的节点上进行， 因为移动计算比移动数据所占的网络资源要少得多。
- 延时调度机制： 实现执行过程优化

### RDD 的设计与运行原理

一个RDD 就是一个分布式对象集合， 一个RDD 可以分为多个分区， 一个分区就是一个数据集片段， 一个RDD 的不同分区可以被保存到集群中的不同节点上， 从而在集群中的不同节点上进行并行分析。

RDD 是只读的记录分区的集合， 不能直接修改， 只能基于稳定的物理存储中的数据集来创建RDD， 或者通过在其他RDD 上执行确定的转换操作来创建RDD。

RDD 提供了一组丰富的操作以支持常见的数据运算， 分为”行动Action” 和“转换Transformation” 两种类型， 前者用于执行计算并指定输出的形式， 后者指定RDD 之间的相互依赖关系。
- 转换操作： 接受一个RDD 并返回一个RDD
- 行动操作： 接受一个RDD 但返回的不是一个RDD

Spark 使用Scala 语言实现RDD 的API：
- RDD 读入外部数据源进行创建。
- RDD 经过一系列的“转换” 操作， 每一次都会产生不同的RDD， 供给下一次“转换” 使用。
- 最后一个RDD 经过“行动Action” 操作进行处理， 并输出到外部数据源。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-20138811a5c16772.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Spark 部署和应用方式

Spark 可运行在YARN 之上， 与Hadoop 进行统一部署， 资源管理和调度依赖YARN， 分布式存储依赖HDFS

![image.png](https://upload-images.jianshu.io/upload_images/13918038-3810b1bfd746e5bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Spark 目前最好与Hadoop 进行统一部署
