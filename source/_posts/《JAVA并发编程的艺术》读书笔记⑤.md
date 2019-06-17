---
title: 《JAVA并发编程的艺术》读书笔记⑤
copyright: true
date: 2019-06-17 09:20:09
tags: JAVA
---

# 双重检查锁定引发的思考

在学习双重版本版本的单例模式的时候， 我书写了如下代码

``` java
public class Singleton {
    private static Singleton uniqueInstance;
    private Singleton() {
    }
    public static Singleton getInstance() {
       //检查实例，如果不存在，就进入同步代码块
        if (uniqueInstance == null) {
            //只有第一次才彻底执行这里的代码
            synchronized(Singleton.class) {
               //进入同步代码块后，再检查一次，如果仍是null，才创建实例
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();  
                }
            }
        }
        return uniqueInstance;
    }
}
```

这段代码表面上很完美， 但是在高并发的环境下， 容易触发里面一个潜在的BUG， 问题的根源出自于**uniqueInstance = new Singleton();  **, 因为在线程读取到uniqueInstance不为null的时候， 该变量引用的对象有可能还没有完成初始化。

<!--more-->

将**uniqueInstance = new Singleton()**拆分为以下三行伪代码：
```JAVA
memory = allocate(); // 1:分配对象的内存空间
ctorInstance(memory); // 2:初始化对象
instance = memory; // 3：设置instance指向刚刚分配的内存空间
```

在Java语言规范中， 所有线程在执行Java程序是必须要遵守intra-thread semantics， 以保证重排序不会改变单线程内的程序执行结果， 重排序在没有改变单线程程序执行结果的前提下， 可以提高程序的执行性能。

单线程下：
![image.png](https://upload-images.jianshu.io/upload_images/13918038-0ef5f26bf4a3f906.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多线程下触发Bug的原因**（线程A执行到uniqueInstance = new Singleton()， 线程B执行到第一个if (uniqueInstance == null)）**：
![image.png](https://upload-images.jianshu.io/upload_images/13918038-15131caa0447a8a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果按上面的时序图执行， 线程B有可能会读取到还没有初始化成功的对象。

#### 基于volatile的解决方案
``` java
public class Singleton {

    //volatile保证，当uniqueInstance变量被初始化成Singleton实例时，多个线程可以正确处理uniqueInstance变量
    private volatile static Singleton uniqueInstance;
    private Singleton() {
    }
    public static Singleton getInstance() {
       //检查实例，如果不存在，就进入同步代码块
        if (uniqueInstance == null) {
            //只有第一次才彻底执行这里的代码
            synchronized(Singleton.class) {
               //进入同步代码块后，再检查一次，如果仍是null，才创建实例
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

当变量声明为**volitle**之后，创建该对象的中所触发的指令重排序， 将会在多线程环境中禁止。
