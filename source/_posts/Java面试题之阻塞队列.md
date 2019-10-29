---
title: Java面试题之阻塞队列
copyright: true
date: 2019-10-28 21:45:23
tags: 阻塞队列
---

# 阻塞队列（BlockingQueue）

- 当阻塞队列为空的时候，从队列中获取元素的操作会被阻塞
- 当阻塞队列为满的时候，向队列中添加元素的操作会被阻塞

## 种类

- **ArrayBlockingQueue**: 由数组结构组成的有界阻塞队列
- **LinkedBlockingQueue**: 由链表结构组成的有界阻塞队列（大小值默认为Integer.MAX_VALUE）
- PriorityBlockingQueue: 支持优先级排序的无界阻塞队列
- DelayQueue：使用优先级队列实现的延迟无界阻塞队列
- **SynchronousQueue**: 不存储元素的阻塞队列，也即有且只有一个元素的队列（一个put 必须要对应一个take）
- LinkedTransferQueue：由链表结构组成的无界阻塞队列
- LinkedBlockingDeque：由列表结构组成的双向阻塞队列

<!--more-->

## BlockingQueue 的核心方法

**抛出异常组**
- add 、remove and element
- - 当阻塞队列为满时，继续add 会抛出IllegalStateException:Queue full
- - 当阻塞队列为空时，继续remove 会抛出NoSuchElementException
- - element 返回队列第一位，但是不会清除

**返回boolean 值组与超时控制**
- offer、poll and peek
- - 当阻塞队列为满时，继续offer 会返回false
- - 当阻塞队列为空时，继续poll 会返回fase
- - peek 返回队列的第一位

**阻塞（官方推荐）**
- put、take
- - 当阻塞队列为满时，继续put 会阻塞等待，队列会一直阻塞生产线程，直到成功put 数据or 中断退出
- - 当阻塞队列为空时，继续take 会阻塞等待，直到队列可用