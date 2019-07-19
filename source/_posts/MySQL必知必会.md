---
title: MySQL必知必会
copyright: true
date: 2019-07-15 08:59:33
tags: MySQL
---

# MySQL 必知必会

- MySQL 线程池的最佳连接数 = （CPU 核心数 * 2） + 有效磁盘数
- 比较运算符能用 = 就不用 <>, 增加索引的利用率
- 如果只有一条查询结果， 请使用 "LIMIT 1"
- 为列选择合适的数据类型， 能用SMALLINT 就不用INT， 磁盘和内存消耗越小越好。
- 将大的DELETE、 UPDATE or INSERT 查询拆成多个小的查询
- 如果结果集允许重复， 则使用UNION ALL 代替 UNION
- 为了充分利用查询缓存， 请保持SQL 语句查询条件的一致性
- 尽量避免使用select *
- where、 Join and Order by 语句的的列尽量被索引
- 使用LIMIT 实现分页逻辑， 减少不必要的传输
