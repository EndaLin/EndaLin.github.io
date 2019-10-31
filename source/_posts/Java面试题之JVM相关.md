---
title: Java面试题之JVM相关
copyright: true
date: 2019-10-29 17:24:09
tags: JVM
---
# GC

## GC Roots

GC Roots 是tracing GC 的根集合，是一组必须活跃的应用

通过一系列名为“GC roots” 的对象为起始点，从这个被成为“GC roots” 的对象向下搜索，如果一个对象到GC roots 没有任何的引用链相连，则说明该对象不可用。也就是说，以给定的一个集合的引用作为根出发点，通过引用关系遍历对象图，能被遍历到的（可到达的对象）则被判定为存活，不可达的则被判定为死亡。

## GC roots set

- 虚拟机栈（栈帧中的局部变量表）中应用的对象
- 方法区中的类静态属性应用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI (Native 方法) 应用的对象

## Tracing 跟踪
- 复制
- 标记 - 清除
- 标记 - 压缩


## JVM

###  查看当前运行程序的配置
``` bash
jinfo -flag 配置项 进程编号

or

jinfo -flags
```

### XX 参数

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

主要是监控对象的GC 状态

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