---
title: 《JAVA并发编程的艺术》读书笔记④
copyright: true
date: 2019-06-03 09:24:05
tags: JAVA
---

# 数据依赖性
![image.png](https://upload-images.jianshu.io/upload_images/13918038-11fa21b09576a040.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果两个操作之间访问同一个变量，且这两个操作有一个为写操作，此时这两个操作就存在数据依赖性。

编译器和处理器在重排序时，会遵守数据依赖性，编译器和处理器不会改变存在数据依赖关系的两个操作的执行顺序。

这里所说的数据依赖性针对单个处理器中狮子那个的指令序列和单个线程中执行的操作，不同处理器之间和不同线程之间的数据依赖性不被编译器和处理器考虑。


# as-if-serial语义

as-if-serial意思就是说：不管怎么重排序（编译器和处理器为了提高并行度），单线程程序的执行结果不能改变。


# 同步程序的顺序一致性效果
![image.png](https://upload-images.jianshu.io/upload_images/13918038-057638a8c7f6a0d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

JMM在具体实现的基本方针是：在不改变正确同步的程序执行结果的前提下，尽可能地为编译器和处理器的优化打开方便之门。


JMM不保证对64位的long型和double型变量的写操作具有原子性。因为当JVM在一些32位的处理器上运行的时候，如果对64为数据的写操作要求原子性，会有比较大的开销，所以JVM可能会把一个64位的long/double写操作拆分成两个32位的写操作的来执行，这两个32位的写操作可能会被分配到不同的总线事务中，此时对这个64位的变量的写操作不具有原子性。
