---
title: Redis
copyright: true
date: 2019-06-09 09:35:35
tags: Nosql
---

# 简介
Redis使用C语言开发的一个开源的高性能键值对(key-value)数据库,它通过提供多种键值数据类型来适应不同场景下的存储需求,目前Redis支持的键值数据类型如下:
- 字符串类型
- 散列类型
- 列表类型
- 集合类型
- 有序集合类型

<!--more-->

# 应用场景
- 缓存(主要用途)
- 分布式集群架构中的session分离
- 聊天室的在线好友列表
- 任务队列
- 应用排行榜
- 网络访问统计
- 数据过期处理
- 数字的自增(高并发下,订单号为yyyymmddHHmmsss001,使用Redis的自增功能,避免重复)


# Redis HA方案
HA(High Available, 高可用性集群)集群系统的简称, 是 保证业务连续性的有效解决方案, 一般有两个或两个以上的结点,分为活动结点以及备用结点, 若活动结点出现了问题,导致正在运行的业务不能正常运行,备用结点此时就能侦测得到,并立即代替活动结点来执行业务,从而实现业务的不中断或短暂中断.

Redis一般以主/从方式部署,官方推荐使用sentinel(哨兵)来实现高可用.

sentinel是解决HA问题,cluster是解决主从复制问题.

![](https://www.funtl.com/assets/20150620161606990.jpg)

Redis集群可以在一组Redis节点之间实现高可用性,在集群中有一个master和多个slave节点,当master节点失效时,应选举出一个slave节点作为新的master, 然后Redis本身并没有自动发现故障并且进行主从切换的能力, 需要外部的监控方案来实现自动故障恢复.

Redis Sentinel是官方推荐的高可用性解决方案,它是Redis集群的监控管理工具,可以提供节点监控.通知,自动故障恢复和客户端配置发现服务.

![](https://www.funtl.com/assets/18841d5327556bfa750148943011901d1eac3cd8.jpg)

# 搭建Redis集群
```yml
version: '3.1'
services:
        master:
                image: redis
                container_name: redis-master
                ports:
                        - 6379:6379
        slave1:
                image: redis
                container_name: redis-slave-1
                ports:
                        - 6380:6379
                command: redis-server --slaveof redis-master 6379
        slave2:
                image: redis
                container_name: redis-slave-2
                ports:
                        - 6381:6379
                command: redis-server --slaveof redis-master 6379
```

# 搭建Redis Sentinel集群
```yml
version: '3.1'
services:
  sentinel1:
    image: redis
    container_name: redis-sentinel-1
    ports:
      - 26379:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel1.conf:/usr/local/etc/redis/sentinel.conf

  sentinel2:
    image: redis
    container_name: redis-sentinel-2
    ports:
      - 26380:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel2.conf:/usr/local/etc/redis/sentinel.conf

  sentinel3:
    image: redis
    container_name: redis-sentinel-3
    ports:
      - 26381:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel3.conf:/usr/local/etc/redis/sentinel.conf
```

#### redis sentinel.conf
```
port 26379
dir /tmp

# 自定义集群名,其中127.0.0.1为Redis-master的IP,6379为redis-master的端口,2为最小投票数
# 如果使用docker部署, 切记别使用127.0.0.1, 要具体的IP地址
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
```

#### 相关命令
```
redis-cli -p 26379  
sentinel master mymaster
sentinel slaves mymaster
```


# lettcue
依赖
```
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
application.yml
```yml
spring:
  redis:
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        max-wait: -1ms
        min-idle: 0
    sentinel:
      master: mymaster
      nodes: 192.168.75.140:26379, 192.168.75.140:26380, 192.168.75.140:26381
```
接口
```java
public interface RedisService {
    public void set(String key, Object value, long seconds);
    public Object get(String key);
}
```


# 参考
[李卫民的教学视频](https://www.bilibili.com/video/av36042649/?p=116)
