---
title: 《JAVA并发编程的艺术》读书笔记⑥
copyright: true
date: 2019-06-17 14:49:49
tags: JAVA
---

# Java线程相关

#### 线程优先级

在Java线程中，通过一个整型成员变量priority来控制优先级， 优先级分为从1-10， 在线程构建的时候可以通过setPriority(int)方法来修改优先级， 默认优先级是5， 优先级高的线程分配时间片的数量要多余优先级低的线程。

针对阻塞频繁的线程需要设置较高的优先级。

偏重计算的线程设置较低的优先级。


#### 线程的状态

- NEW： 初始状态， 线程被构建， 但是还没有调用start()方法
- RUNNABLE： 运行状态， Java线程将操作系统中的就绪和运行两种状态笼统地称作“运行中”
- BLOCKED：阻塞状态，表示线程阻塞干锁
- WAITING：等待状态， 表示线程进入等待状态，进入该状态的线程表示当前线程需要等待其他线程作出一些特定的动作
- TIME_WAITING：超时等待状态， 它是可以在指定的时间自行返回
- TERMINATED：终止状态，表示当前线程已经执行完毕

![image.png](https://upload-images.jianshu.io/upload_images/13918038-7a5d39835efdf469.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### Daemon线程
Daemon线程是一种支持型线程，因为它主要被用作程序中后台调度以及支持工作，当一个JAVA虚拟机中不存在非Daemon线程的时候，JAVA虚拟机将会退出。

可以使用**Thread.setDaemon(true)**来设置Daemon线程。

Deamon线程被用作支持性工作，但是在JAVA虚拟机退出时，Daemo线程中的finally块不一定会执行。

#### 对象、监视器、同步队列和执行线程之间的关系
![image.png](https://upload-images.jianshu.io/upload_images/13918038-e29a96befabb7a49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 线程间的通信
#### 等待/通知机制
![image.png](https://upload-images.jianshu.io/upload_images/13918038-3625a595fa1e836f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有一些细节上的东西需要注意一下：
- 使用wait、notify、notifyAll时需要对调用对象加锁。
- 调用wait方法后，线程状态由RUNNING变成WAITING，并将当前线程放置在对象的等待队列中。
- notify和notifyAll调用后，等到线程依旧不会从wait返回，而是要等本线程释放锁之后，等待线程才有机会返回
- notify是将等待队列中的一个等待线程从等待队列中移到同步队列中
- notifyAll是将等待队列中所有的线程全部移到同步队列中，被移动的线程状态由WAITING变为BLOKCED
- 从wait方法返回后重新执行的前提是获得对象的锁
