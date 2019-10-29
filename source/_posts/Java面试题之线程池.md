---
title: Java面试题之线程池
copyright: true
date: 2019-10-29 10:34:06
tags: 线程池
---

# Runnable and Callable

- Runnable 没有返回值
- Callable 有返回值

```java
class MyThread implements Runnable {
    @Override
    public void run () {

    }
}

class MyThread2 implements Callable<Integer> {
    @Override
    public Integer call() {
        return 123;
    }
}
public class Test {
    public static void main(String[] args) throws Exception {
        FutureTask<Integer> futureTask = new FutureTask<>(new MyThread2());

        Thread thread = new Thread(futureTask, "aa");
        thread.start();

        // 阻塞等待线程执行结果
        System.out.println(futureTask.get());
    }
}
```

<!--more-->

# 线程池
## 线程池优势
- **降低资源消耗**。 通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。 当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性**。 线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，
- **使用线程池可以进行统一的分配，调优和监控**。

## 线程池的创建
### Executors
- Executors.newFixThreadPool(n) : 创建指定个数的线程池
- Executors.newSingleThreadExecutor: 创建只有一个线程的线程池
- Executors.newCacheThreadPool: 适用于不定个数的短期任务的执行，通过内部的调度，根据实际的情况动态地调整线程池中的线程数量

部分底层创建源码（ThreadPoolExecutor）
```java
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }

    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }

    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```

**注意**
- submit有返回值，而execute没有  
[ExecutorService的execute和submit方法](https://blog.csdn.net/peachpi/article/details/6771946)


### ThreadPoolExecutor(阿里推荐)

```java
       /**
     * Creates a new {@code ThreadPoolExecutor} with the given initial
     * parameters.
     *
     * @param corePoolSize the number of threads to keep in the pool, even
     *        if they are idle, unless {@code allowCoreThreadTimeOut} is set
     * @param maximumPoolSize the maximum number of threads to allow in the
     *        pool
     * @param keepAliveTime when the number of threads is greater than
     *        the core, this is the maximum time that excess idle threads
     *        will wait for new tasks before terminating.
     * @param unit the time unit for the {@code keepAliveTime} argument
     * @param workQueue the queue to use for holding tasks before they are
     *        executed.  This queue will hold only the {@code Runnable}
     *        tasks submitted by the {@code execute} method.
     * @param threadFactory the factory to use when the executor
     *        creates a new thread
     * @param handler the handler to use when execution is blocked
     *        because the thread bounds and queue capacities are reached
     * @throws IllegalArgumentException if one of the following holds:<br>
     *         {@code corePoolSize < 0}<br>
     *         {@code keepAliveTime < 0}<br>
     *         {@code maximumPoolSize <= 0}<br>
     *         {@code maximumPoolSize < corePoolSize}
     * @throws NullPointerException if {@code workQueue}
     *         or {@code threadFactory} or {@code handler} is null
     */
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.acc = System.getSecurityManager() == null ?
                null :
                AccessController.getContext();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }

```

**七大参数**
- corePoolSize: 线程池中常驻核心线程数
- maximumPoolSize: 线程池能够容纳同时执行的最大线程数，此值必须大于等于1
- keepAliveTime：多余的空闲线程的存活时间，当前的线程池的数量超过corePoolSize 时，当空闲时间达到keepAliveTime 值时，多余的空闲线程会被销毁直到剩下corePoolSize 个线程为止。
- unit：keepAliveTime 的单位
- workQueue：阻塞任务队列，被提交但尚未被执行的任务（当任务队列满时，线程池会扩充线程数，扩充后的线程数会小于maximumPoolSize）
- threadFactory：表示生成线程的线程工厂，一般使用默认便可
- RejectedExecutionHandler：拒绝策略，当阻塞队列已满而且活跃线程数已经达到了maximumPoolSize 后，会启动拒绝策略，拒绝请求的加入
- - AbortPolicy(默认)：该策略直接抛出RejectedExecutionException 异常阻止系统的正常运行
- - CallerRunsPolicy：该策略不会抛弃任务也不会抛出异常，而是将任务退回到调用者
- - DiscardOldestPolicy：抛弃队列中等待最久的任务，然后把当前任务加入到队列中再次提交该任务
- - DiscardPolicy：直接丢弃任务，不给予任何的处理，也不抛出异常。


```java
        ExecutorService executorService = new ThreadPoolExecutor(
                2, 
                5, 
                5L, 
                TimeUnit.SECONDS, 
                new LinkedBlockingQueue<Runnable>(3), 
                Executors.defaultThreadFactory(), 
                new ThreadPoolExecutor.AbortPolicy());
```

### 线程池的工作流程
- 在创建了线程池后，等待提交过来的任务请求
- 当调用了execute() 方法添加了一个请求任务后，线程池会做如下判断：
- - 如果正在运行的线程池数量小于corePoolSize，那么马上创建线程运行这个任务
- - 如果正在运行的线程数量大于或等于corePoolSize时，那么会将请求任务放到阻塞队列中
- - 如果阻塞队列已经满了，而且正在运行的线程数量小于maximumPoolSize，那么会创建非核心线程来立即运行这个任务
- - 如果阻塞队列已经满了，而且正在运行的线程数量大于或等于maximumPoolSize，那么线程池会启动拒绝策略
- 当一个线程执行完任务时，会从队列中取出下一个任务出来继续执行
- 当一个线程的空闲时间大于kiveAliveTime 时，会关闭一些线程，直到线程数达到corePoolSize 为止


几张参考图
![](https://upload-images.jianshu.io/upload_images/2177145-33c7b5ff75cf2bf7.png?imageMogr2/auto-orient/strip|imageView2/2/w/826/format/webp)

![](https://upload-images.jianshu.io/upload_images/3096083-2286629fbc77d3b5.png?imageMogr2/auto-orient/strip|imageView2/2/w/708/format/webp)

### 线程池线程数的合理配置

#### CPU 密集型

CPU 密集型需要大量的计算，没有阻塞，CPU 一直在全速运行

一个公式：CPU 核数 + 1个线程的线程池

#### IO 密集型

IO　密集型，需要大量的IO　操作，即大量的阻塞

在单线程上运行IO　密集型的任务会导致浪费大量CPU　运算能力浪费在等待上，所以在IO　密集型任务中运行多线程k可以大大加速程序的运行

参考公式：CPU　核数／（１－阻塞系数），阻塞系数在0.8-0.9 之间

# 死锁的编码以及定位分析

```java
class Resource implements Runnable{
    private String lockA;
    private String lockB;

    public Resource (String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run () {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + " 获得" + lockA);
            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + " 获得" + lockB);
            }
        }
    }
}

public class Test {
    public static void main(String[] args) throws Exception {
        String lockA = "lockA";
        String lockB = "lockB";

        new Thread(new Resource(lockA, lockB), "A").start();
        new Thread(new Resource(lockB, lockA), "B").start();
    }
}
```

## 定位分析(jps + jstack)

```bash
Java stack information for the threads listed above:
===================================================
"B":
        at Resource.run(Test.java:20)
        - waiting to lock <0x000000076b316f18> (a java.lang.String)
        - locked <0x000000076b316f50> (a java.lang.String)
        at java.lang.Thread.run(Thread.java:748)
"A":
        at Resource.run(Test.java:20)
        - waiting to lock <0x000000076b316f50> (a java.lang.String)
        - locked <0x000000076b316f18> (a java.lang.String)
        at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.

```