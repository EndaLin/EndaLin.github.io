---
title: Netty学习笔记
copyright: true
date: 2019-07-18 13:39:42
tags: Netty
---

# Netty in action

## Channel、 EventLoop和 ChannelFuture

- Channel -> socket
- EventLoop -> 控制流、多线程处理、并发
- ChannelFuture -> 异步通知

### Channel 接口

基本的I/O 操作：（bind()、 connect()、 read()、 和write()）

在基于Java 的网络编程中， 其基本构造是class socket, Netty 的Channel 接口提供的API， 大大降低了直接使用socket 的难度。 此外， Channel 也是拥有许多预定义、 专门实现的广泛类层次结构的根

- EmbeddedChannel
- LocalServerChannel
- NioDatagramChannel
- NioSctpChannel
- NioSocketChannel

**Channel 的生命周期**
- ChannelUnRegistered：Channel 已被创建， 但是还没有注册到EventLoop 中
- ChannelRegistered：Channel 已经被注册到EventLoop 中
- ChannelActive：Channel 处于活动状态， 已经连接到远程节点， 可以接收或者发送数据
- ChannelInActive：Channel 没有连接到远程节点

<!--more-->

### EventLoop

EventLoop 定义了Netty 的核心抽象， 用于处理连接的生命周期中所发生的事件。

- 一个EventLoopGroup 包含一个或者多个EventLoop
- 一个EventLoop 在它的生命周期内只有一个Thread 绑定
- 所有由EventLoop 处理的I/O 事件都由它专属的Thread 上被处理
- 一个Channel 在它的生命周期内只注册一个EventLoop
- 一个EventLoop 可能被分配给以一个或者多个Channel

注意：一个给定的Channel 的I/O 操作都是由相同的Thread 去执行的。

![image.png](https://upload-images.jianshu.io/upload_images/13918038-27236ab88c913b97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/13918038-81988e248b7dc191.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### ChannelFuture

Netty 所有操作都是异步的， Netty 提供了ChannelFuture 接口， 其addListener() 方法注册了一个ChannelFutureListener， 以便在一个操作完成之后作一个及时的反馈。


## ChannelHandler 和 ChannelPipeline

### ChannleHandler

ChannelHandler 充当了所有处理入站和出站数据的应用程序逻辑的容器

例如：
- ChannelHandlerAdapter
- ChannelInboundHandler：处理入站数据以及各种状态变化， 其中一个子类是SimpleChannelInboundHandler<T>
- ChannelOutboundHandlerAdapter：处理出站数据并且允许拦截所有的操作
- ChannelDuplexHandler

**ChannelHandler 生命周期方法**
- handlerAdded：当ChannelHandler 添加到ChannelPipeline 时被调用
- handlerRemoved：当从ChannelPipeline 中移除ChannelHandler 时被调用
- exceptionCaught：当处理过程中， 在拦截链中遇到异常时被触发

**ChannelInboundHandler**

当某个 ChannelInboundHandler 的实现重写 channelRead()方法时，它将负责显式地
释放与池化的 ByteBuf 实例相关的内存。

Netty 为此提供了一个实用方法 ReferenceCountUtil.release(msg)来释放资源

SimpleChannelInboundHandler 会自动释放资源，

![image.png](https://upload-images.jianshu.io/upload_images/13918038-ddd2e358d40eba04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**ChannelOutboundHandler**

ChannelPromise与ChannelFuture ChannelOutboundHandler中的大部分方法都需要一个
ChannelPromise参数，以便在操作完成时得到通知。ChannelPromise是ChannelFuture的一个
子类，其定义了一些可写的方法，如setSuccess()和setFailure()，从而使ChannelFuture不可变

![image.png](https://upload-images.jianshu.io/upload_images/13918038-4085ffe8c8f9466c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 解码器和编码器
所有由 Netty 提供的编码器/解码器适配器类都实现
了 ChannelOutboundHandler 或者 ChannelInboundHandler 接口

对于入站数据来说，channelRead 方法/事件已经被重写了。对于每个从入站
Channel 读取的消息，这个方法都将会被调用。随后，它将调用由预置解码器所提供的 decode()
方法，并将已解码的字节转发给 ChannelPipeline 中的下一个 ChannelInboundHandler。
出站消息的模式是相反方向的：编码器将消息转换为字节，并将它们转发给下一个
ChannelOutboundHandler。

#### 抽象类 SimpleChannelInboundHandler<T>

T 是你要处理的消息的 Java 类型

### ChannelPipeline(拦截过滤器)

ChannelPipeline 提供了ChannelHandler 链的容器， 并定义了用于在该链上传播入站和出站事件流的API

ChannelPipeline 持有所有将应用于入站和出站数据以及事件的 ChannelHandler 实
例，这些 ChannelHandler 实现了应用程序用于处理状态变化以及数据处理的逻辑

当Channel 被创建之后， 它会被分配到专属的Channelpeline

ChannelHandler 安装到ChannelPipeline 的过程如下：
- 一个ChannelInitializer 的实现被注册到了ServerBootstrap 中
- 当ChannelInitializer.initChannel() 方法被调用之后， ChannelInitializer 将会在ChannelPipeline 中安装一组自定义的ChannelHandler
- ChannelInitializer 将它自己从ChannelPipeline 中移除

![image.png](https://upload-images.jianshu.io/upload_images/13918038-bf92f1b5590bde05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在Netty 中， 有两种写数据的方式：
- 直接写到Channel： 这种方式会导致消息从ChannelPipeline 尾部开始流动
- 写到和ChannelHandler 相关联的ChannelHandlerContext 中： 这种方式会导致消息从ChannelPipeline 对应的下一个ChannelHandler 开始流动

![image.png](https://upload-images.jianshu.io/upload_images/13918038-e94673142bdb8a6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/13918038-18abb7df860271df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### ChannelHandlerContext

ChannelHandlerContext 代表了ChannelHandler 和 ChannelPipeline 之间的关联, 每当有ChannelHandler 添加到ChannelPipeline 中时，都会创建ChannelHandlerContext

ChannelHandlerContext 的主要功能是管理它所关联的ChannelHandler 和在同一个ChannelPipeline 中的其他ChannelHandler 之间的交互

注意：
如果调用Channel 和ChannelPipeline 上面的方法， 它们会沿着这个ChannelPipeline 上进行传播

如果ChannelHandlerContext 上相同的方法， 只会从当前ChannelHandler 开始传播

![image.png](https://upload-images.jianshu.io/upload_images/13918038-6d8e9e97109f88f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过 ChannelHandlerContext 获取到 Channel 的引用。调用Channel 上的 write()方法将会导致写入事件从尾端到头部地流经 ChannelPipeline

## 引导

两种类型： 客户端（Bootstrap）与服务器端(ServerBootStrap)

## 传输

![image.png](https://upload-images.jianshu.io/upload_images/13918038-8975f267e8b5578c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## ByteBuf

Netty 的ByteBuf 用于替代Java NIO 的ByteBuff

ByteBuf 维护了两个不同的索引： 一个用于读取、一个用于写入

![image.png](https://upload-images.jianshu.io/upload_images/13918038-071fb67cd185fd83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## ByteBuf 的使用模式

### 堆缓冲区

最常用的ByteBuf 模式是将数据存储在JVM 的堆空间里面， 这种模式被称为支撑数组， 它能在没有使用池化的情况下提供快速的分配和释放。

### 直接缓冲区

直接缓冲区的内容将驻留在常规的会被垃圾回收的堆之外

直接缓冲区是网络数据传输的理想选择， 但不是数据处理的理想选择， 因为在直接缓冲区需要被处理的数据， 需要复制到堆中才能被处理

如果你的数据包含在一个在堆中分配的缓冲区， 在通过套接字发送之前， JVM 会将它复制到直接缓冲区， 然后再发送

### 复合缓冲区

它为多个ByteBuf 提供一个聚合视图， 可以根据需要添加或者删除ByteBuf 实例

Netty 通过一个ByteBuf 子类（compositeByteBuf）实现这个模式， 它提供了一个将多个缓冲区表示为单个合并缓冲区的虚拟表示， 所以这里值得注意的是， CompositeByteBuf 实例可能同时包含直接内存和非直接内存。
