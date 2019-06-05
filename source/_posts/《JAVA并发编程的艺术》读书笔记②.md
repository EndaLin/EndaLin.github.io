---
title: 《JAVA并发编程的艺术》读书笔记②
copyright: true
date: 2019-06-01 18:44:37
tags: JAVA
---

# 前言
JAVA代码在编译之后会变成JAVA字节码，字节码被类加载器加载到JVM里面，JVM执行字节码，将字节码装换成汇编指令在CPU上执行。

JAVA中所执行的并发机制依赖JVM的实现和CPU的指令。


# volatile
在多线程并发编程中，volatile是轻量级的synchronized，它在多处理器开发中保证了共享变量的可见性，如果volatile变量修饰符使用恰当，它比synchronized的使用或执行成本更低，因为它不会引起线程上下文的切换和调度。

#### 作用1（保证共享变量的可见性）
- 将当前处理器缓存行的数据写会到系统内存中。
- 这个写会内存的操作会使其他CPU里缓存了该内存地址的数据无效。

#### 对作用1的解释：
为了提高处理速度，处理器不直接与内存进行通信，而是将内存中的数据读到内部的缓存里面再进行操作，而为了保持缓存的一致性，在多处理器模式下，就实现缓存一致性的协议，每个处理器通过嗅探在总线上传播的数据来检查自己缓存的值是否已经过期，当处理器发现自己缓存行对应的内存地址被修改，将会将当前处理器的缓存行设置为无效状态，当处理器对这个数据进行修改操作的时候，就会重新从系统内存中把数据督导处理器缓存中。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-0fc8744f2e22d249.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/13918038-45e0d382f422f64e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### volatile实现原则
- Lock前缀指令会引起处理器缓存写回到内存中。
- 一个处理器的缓存回写到内存中会导致其他处理器的缓存无效。

#### 特性
- 可见性：对一个Volatile变量的读，总是能够看到任意线程对这个volatile的最后写入。
- 原子性：对任意单个volatile的变量的读写具有原子性，但是类似于volatile++这种复合操作不具有原子性。

#### volatile写-读建立的happens-before关系
- 从内存语义的角度来说，volatile的写-读与锁的释放-获取有相同的内存效果。

# synchronized
- 对于普通同步方法，锁是当前实例对象。
- 对于静态同步方法，锁是当前类的Class对象。
- 对于同步方法块，锁是synchronized括号里配置的对象。

# 原子操作
原子操作意思就是不可被中断的一个或一系列的操作。

#### 处理器保证原子操作的措施
- 总线锁：举个例子，当CPU获取到共享变量时，会发出LOCK#信号去组织其它处理器对这个变量的请求
- 缓存锁：
