---
title: Hadoop学习笔记⑤
copyright: true
date: 2019-06-29 09:21:33
tags: Hadoop
---

# 云计算

云计算是分布式计算、并行计算、效用计算、网络存储、虚拟化、负载均衡等计算机和网络技术发展融合的产物。

云计算主要包括3种类型： IaaS、PaaS 和SaaS。


# 云数据库

云数据库是部署和虚拟化在云计算环境中的数据库。

# MapReduce

思想: 分而治之， 把一个大的数据块拆分为多个小的数据块在不同的机器上并行处理。

<!--more-->

Map - Reduce 任务： 通常运行为数据存储节点上， 由此可使计算任务和数据存储在同一个节点之上， 无需额外的数据传输开销， 当Map 任务结束之后， 会生成以<K, V> 形式的中间结果， 然后分配给多个Reduce 任务去执行， 具有相同的Key 的任务会被分配到同一个Reduce 任务， Reduce 任务会对中间结果进行处理， 得到最终结果， 然后输出的分布式配置文件中。

注意： 不同的Map 任务之间以及不同的Reduce 任务之间是不会发生任何的信息交换。

#### 工作流程

- 首先使用InputFormat 模块做Map 前的预处理， 然后将文件切分为**逻辑上多个InputSplit**， InputSplit 是Map Reduce 对文件进行处理和运算的处理单位， 只是一个逻辑概念， 并没有进行实际的切分， 只是记录了需要处理的数据的位置和长度。

- 由于InputSplit 是逻辑分而不是物理分， 所以还需要通过RecordReader 根据InputSplit 中的信息来处理InputSplit 中的具体信息， 加载数据并转换成适合Map 任务读取的键值对.

- Map 任务会根据用户自定义的映射规则， 输出一系列的<K, V> 作为中间结果

- 在将Map 任务输出的中间结果交给Reduce 任务处理之前， 需要经过shuffle 处理， 从无序的<key, value> 到有序的<key, value-list>。

- Reduce 以一系列<key, value-list> 作为输入， 按照用户定义的逻辑， 将处理结果输出到OutFormat 模块。

- OutputFormat 会验证输出目录是否存在, 或者输出类型是否符合配置文件中的配置类型, 如果都满足则输出到HDFS 中.

![image.png](https://upload-images.jianshu.io/upload_images/13918038-ad73fa8729eb659d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
