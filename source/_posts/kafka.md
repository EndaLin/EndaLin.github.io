---
title: kafka
copyright: true
date: 2019-10-08 17:21:03
tags: kafka
---

# kafka

kafka 是一个分布式消息队列，kafka 对消息保存是根据Topic 进行归类，发送者称为Producer，消息接受者称为Consumer，kafka 集群有多个kafka 实例组成，每个实例称为 broker

无论是kafka 集群还是consumer 都依赖Zookeeper 集群保存一些meta 信息， 来保证系统的可用性
