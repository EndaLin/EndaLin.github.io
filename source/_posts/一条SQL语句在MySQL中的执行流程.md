---
title: 一条SQL语句在MySQL中的执行流程
copyright: true
date: 2019-06-02 16:24:55
tags: MySQL
---

# MySQL 架构分析
首先，我们需要了解MySQL的一个简要的架构
- 连接器： 身份验证与权限相关
- 查询缓存：执行查询语句时，会先查询缓存（失效率过高，不推荐用）
- 分析器：分析SQL语句
- 优化器：优化SQL语句
- 执行器：执行SQL语句，并从存储引擎用返回数据

![image.png](https://upload-images.jianshu.io/upload_images/13918038-ee859e1716a3b4e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

值得注意的是，Mysql分为了Server层和存储引擎层：
- Server层： 主要包括连接器、查询缓存、分析器、优化器和执行器等组件，很多功能都是在这一层实现的，如存储过程、触发器、视图、函数等等，还有一个日志模块binglog
- 存储引擎模块：支持InnoDB、MyISAM等等，InnoDB为默认的存储模块。

# MySQL的日志模块
这里，我们需要特别主义redo log 和 binlog这两个日志模块，前者是InnoDB自带的日志模块，后者是MySQL自带的日志模式。

#### SQL 语句的类型
SQL语句的类型主要分为两种
- 查询： select
- 更新：update、delete

#### 执行更新语句时，该如何操作这两个日志模块
```sql
update tb_student set name = 'wt' where name = 'name'
```
- 首先，要根据where查询到对应那一条数据
- 根据查询到的数据，先进行修改，然后调用引擎接口，写入这一行数据，InnoDB会把数据保存在缓存中，同时记录redo log,此时redo log 进入prepare状态，然后告诉执行器，执行结束
- 执行器收到通知后记录binlog，然后调用引擎接口，提交redo log为提交状态
- 更新完成

# 小结
- MySQL 主要分为 Server 层和引擎层，Server 层主要包括连接器、查询缓存、分析器、优化器、执行器，同时还有一个日志模块（binlog），这个日志模块所有执行引擎都可以共用,redolog 只有 InnoDB 有。
- 引擎层是插件式的，目前主要包括，MyISAM,InnoDB,Memory 等。
- 查询语句的执行流程如下：权限校验（如果命中缓存）---》查询缓存---》分析器---》优化器---》权限校验---》执行器---》引擎
- 更新语句执行流程如下：分析器----》权限校验----》执行器---》引擎---redo log(prepare 状态---》binlog---》redo log(commit状态)
