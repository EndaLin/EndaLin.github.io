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

- ArrayBlockingQueue: 由数组结构组成的有界阻塞队列
- LinkedBlockingQueue: 由链表结构组成的有界阻塞队列（大小值默认为Integer.MAX_VALUE）
- PriorityBlockingQueue: 支持优先级排序的无界阻塞队列
- DelayQueue：使用优先级队列实现的延迟无界阻塞队列
- SynchronousQueue: 不存储元素的阻塞队列，也即单个元素的队列
- LinkedTransferQueue：由链表结构组成的无界阻塞队列
- LinkedBlockingDeque：由列表结构组成的双向阻塞队列