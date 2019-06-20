---
title: hadoop对海量数据处理的解决思路
copyright: true
date: 2019-06-20 11:39:06
tags: hadoop
---

# 如何解决海量数据的存储问题
![image.png](https://upload-images.jianshu.io/upload_images/13918038-9a096bb5fec1d19b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 如何解决海量数据的计算

MapReduce

Map: 计算本地数据， 在各个结点下计算， 本地并发

Reduce: 全局处理， 可以分组处理， 对各个Map的中间结果进行处理
