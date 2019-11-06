---
title: Java面试题之JVM相关
copyright: true
date: 2019-10-29 17:24:09
tags: JVM
---

# JVM 堆

在Java 中，堆被划分为两个不同的区域：**新生代和老年代**

**新时代**：被划分为三个区域：Eden、From survivor 和 To survivor

划分的目的是为了使JVM 更好地管理JVM 内存对象，包括内存的分配以及内存的回收

![](https://img-blog.csdn.net/20151115124816485)


## 参考
[Java GC、新生代、老年代](https://blog.csdn.net/gyqjn/article/details/49848473)

# GC

## GC Roots

GC Roots 是tracing GC 的根集合，是一组必须活跃的应用

通过一系列名为“GC roots” 的对象为起始点，从这个被成为“GC roots” 的对象向下搜索，如果一个对象到GC roots 没有任何的引用链相连，则说明该对象不可用。也就是说，以给定的一个集合的引用作为根出发点，通过引用关系遍历对象图，能被遍历到的（可到达的对象）则被判定为存活，不可达的则被判定为死亡。

## GC roots set

- 虚拟机栈（栈帧中的局部变量表）中应用的对象
- 方法区中的类静态属性应用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI (Native 方法) 应用的对象

## 四大垃圾回收算法
- 引用计数
- 复制拷贝
- 标记-清除算法（Mark-Sweep） （优：节约空间，缺：产生内存碎片）
- 标记 - 压缩

## 主要的垃圾回收器

下图展示了7种作用于不同分代的收集器，如果两个收集器之间存在连线，就说明它们可以搭配使用。虚拟机所处的区域，则表示它是属于新生代收集器还是老年代收集器。Hotspot实现了如此多的收集器，正是因为目前并无完美的收集器出现，只是选择对具体应用最适合的收集器。

![](https://pic.yupoo.com/crowhawk/56a02e55/3b3c42d2.jpg)

- 串行垃圾回收器（-XX:+UseSerialGC）
> 串行垃圾回收器通过持有应用程序所有的线程进行工作。它为单线程环境设计，只使用一个单独的线程进行垃圾回收，通过冻结所有应用程序线程进行工作，所以可能不适合服务器环境。它最适合的是简单的命令行程序（单CPU、新生代空间较小及对暂停时间要求不是非常高的应用）。是client级别默认的GC方式。
>
> 开启之后会使用，新时代和老年代都会使用串行回收收集器，新生代使用标记-复制算法，老年代使用标记-整理算法

![](https://pic.yupoo.com/crowhawk/6b90388c/6c281cf0.png)

- ParNew 收集器（-XX:+UseParNewGC）
> 只对新生代起作用
> ParNew 回收器也要冻结用户的工作线程，在新时代中使用多线程来运行标记-复制算法来进行垃圾回收，在老年期还是使用单线程和标记-整理算法来进行垃圾回收  
**注意**： ParNew 和SerialOld 已经不再被推荐使用
**备注**：-XX:ParallelGCThreads 限制线程数量，默认起与CPU 相同数目的线程

![](https://pic.yupoo.com/crowhawk/605f57b5/75122b84.png)

- Parallel Scavenge 收集器（-XX:+UseParallelGC）
> 它是JVM的默认垃圾回收器。与串行垃圾回收器不同，它使用多线程进行垃圾回收。相似的是，当执行垃圾回收的时候它也会冻结所有的应用程序线程。  
> 新生代和老年代都用多个线程并行去运行
![](https://pic.yupoo.com/crowhawk/9a6b1249/b1800d45.png)


- CMS并发标记扫描垃圾回收器（-XX:+UseConcMarkSweepGC）
> 用户线程和垃圾回收线程同时执行，适用于对响应时间有要求的公司  
> 只要在老年代开启了CMS，新生代会自动开启ParNew，Serial Old会作为CMS 出错的后备收集器
>
> **四个步骤**:
> - 初始化标记Initial Mark（冻结用户线程）：只是标记一下GC Roots能直接关联的对象，速度很快
> - 并发标记Concurrent Mark（GC 线程和用户线程一起工作）：进行GC Roots跟踪过程，这是主要的标记过程，标记所有的对象
> - 重新标记Remark（冻结用户线程）：为了修正正在并发标记期间，因用户程序继续运行而导致标记发生变动的那一部分对象的标记记录，任然需要暂停所有工作线程。（二次确认）
> - 并发清除Concurrent sweep（GC 线程和用户线程一起工作）
>![](https://pic.yupoo.com/crowhawk/fffcf9a2/f60599b2.png)

- G1垃圾回收器（-XX:+UseG1GC）
>  G1垃圾回收器适用于堆内存很大的情况，他将堆内存分割成不同的区域，并且并发的对其进行垃圾回收,不会产生许多内存碎片，G1 在停顿时间添加了预测机制，用户可以指定停顿时间  
> **G1 特点**
> - G1 能充分利用CPU，多核环境的优势，尽量缩短STW
> - G1 整体采用标记-整理算法，局部通过复制算法，不会产生内存碎片
> - 宏观上看G1 不再区分年轻代和老年代，把内存划分为多个Region 区
> - G1 收集器在小范围内进行年轻代和老年代的区分，不再是物理隔离，是一部分Region 的集合而且不需要连续

![](https://upload-images.jianshu.io/upload_images/3789193-f149ab73f33539d4.png?imageMogr2/auto-orient/strip|imageView2/2/w/638/format/webp)

### 如何选择合适的垃圾收集器
- 单CPU 或者小内存，单机程序
> -XX:+UseSerialGC
- 多CPU，需要最大的吞吐量，如后台计算型应用
> -XX:+UseParallelGC or -XX:+UseParallelOldGC
- 多CPU，追求低停顿时间，需要快速响应
> -XX:+UseConcMarkSweepGC -XX:+ParNewGC

### 查看默认的垃圾回收器
```bash
F:\IDEA\Test>java -XX:+PrintCommandLineFlags -version

-XX:G1ConcRefinementThreads=8 -XX:GCDrainStackTargetSize=64 -XX:InitialHeapSize=267006528 -XX:MaxHeapSize=4272104448 -XX:+PrintCommandLineFlags -XX:ReservedCodeCacheSize=251658240 -XX:+
SegmentedCodeCache -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation
openjdk version "11.0.3" 2019-04-16
OpenJDK Runtime Environment (build 11.0.3+12-b304.10)
OpenJDK 64-Bit Server VM (build 11.0.3+12-b304.10, mixed mode, sharing)

```

结果：-XX:+UseG1GC


## 概念补充
- **可控制的吞吐量** = 运行用户代码的时间/（运行用户代码的时间+垃圾收集器），高吞吐量意味着利用CPU 的效率高

## 参考
[浅析JAVA的垃圾回收机制（GC）](https://www.jianshu.com/p/5261a62e4d29)

[深入理解JVM(3)——7种垃圾收集器](https://crowhawk.github.io/2017/08/15/jvm_3/)

[JVM的垃圾回收机制 总结(垃圾收集、回收算法、垃圾回收器)](https://www.cnblogs.com/aspirant/p/8662690.html)

<!--more-->

# JVM

##  查看当前运行程序的配置
``` bash
jinfo -flag 配置项 进程编号

or

jinfo -flags
```

## XX 参数

- Boolean 类型：(+ or - ) 某参数
> case: 是否打印GC 收集细节：-XX:+PrintGCDetails
> ```
> F:\IDEA\Test\src>jps -l
> 2380 Test
>
> F:\IDEA\Test\src>jinfo -flag PrintGCDetails 2380
>-XX:+PrintGCDetails
>```

- KV 设置类型：-XX:key=value
> case: -XX:MetaspaceSize=128m

注意：

- -Xms 等价于 -XX:InitialHeapSize
- -Xmx 等价于 -XX:MaxHeapSize

# 引用

![](https://user-gold-cdn.xitu.io/2018/8/26/16576be9ee015804?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 强引用（Reference）

在Java 中，把一个对象赋值给另一个引用变量，这个引用变量就是一个强应用。当一个对象被强引用变量引用时，它处于可达状态，不会被GC 回收，因此，强应用是造成Java 内存泄漏的主要原因。

## 软引用（SoftReference）



对于软引用对象来说：
- 当系统内存充足时，不会被回收
- 当系统内存不足时，会被回收掉

软引用通常会应用在对内存敏感的程序中

**Case:**

JVM OPTIONS: -Xms5m -Xmx5m -XX:+PrintGCDetails

```java
        Object object = new Object();
        SoftReference<Object> softReference = new SoftReference<>(object);
        // 解除强引用和弱引用的联系
        object = null;

        try {
            /**
             * 模拟 OOM，迫使GC
             */
            byte[] bytes = new byte[30 * 1024 * 1024];
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println(object);
            System.out.println(softReference.get());
        }
输出：
null
null
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at Test.main(Test.java:23)
```

## 弱引用(WeakReference)

只要垃圾回收一运行，无论内存是否足够，GC 一律回收弱引用

弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象。


```java
    public static void main(String[] args) throws Exception {
        Object object = new Object();
        WeakReference<Object> weakReference = new WeakReference<>(object);
        object = null;
        // 手动触发GC
        System.gc();
        System.out.println(weakReference.get());
    }

输出：
null
```

### WeakHashMap

当Key 值变成弱引用对象时，GC 会对其进行清除

```java
        WeakHashMap<Object, Object> weakHashMap = new WeakHashMap<>(1);
        Object o1 = new Object();
        Object o2 = new Object();
        weakHashMap.put(o1, o2);
        // 让key 值对象的内存块变成弱引用对象
        o1 = null;
        System.gc();
        System.out.println(weakHashMap);

输出：
{java.lang.Object@4554617c=java.lang.Object@74a14482}
```

## 虚引用（PhantomReference）

虚引用主要用来跟踪对象被垃圾回收器回收的活动。

> 虚引用必须和引用队列(ReferenceQueue)联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。


```java
public static void main(String[] args) throws Exception {
        Object o1 = new Object();
        ReferenceQueue<Object> referenceQueue = new ReferenceQueue<>();
        PhantomReference<Object> phantomReference = new PhantomReference<>(o1, referenceQueue);
        System.out.println(o1);
        System.out.println(phantomReference.get());
        System.out.println(referenceQueue.poll());

        // GC
        o1 = null;
        System.gc();
        System.out.println("================");

        System.out.println(o1);
        System.out.println(phantomReference.get());
        System.out.println(referenceQueue.poll());
    }

输出：
java.lang.Object@4554617c
null
null
[GC (System.gc()) [PSYoungGen: 1383K->496K(1536K)] 1503K->800K(5632K), 0.0008890 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (System.gc()) [PSYoungGen: 496K->0K(1536K)] [ParOldGen: 304K->640K(4096K)] 800K->640K(5632K), [Metaspace: 3440K->3440K(1056768K)], 0.0065375 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
================
null
null
java.lang.ref.PhantomReference@74a14482
```

# 参考
[理解Java的强引用、软引用、弱引用和虚引用](https://juejin.im/post/5b82c02df265da436152f5ad)