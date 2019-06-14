---
title: Spring相关知识
copyright: true
date: 2019-06-12 16:26:14
tags: Spring
---

# Spring 模块（4.x）
最新的5.x版本中Web模块的Portlet已经废弃， 同时增加了异步响应式处理的WebFlux组件。

![](https://camo.githubusercontent.com/d7846803a95e57f57a94197168714bfc58db6432/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d362f537072696e672545342542382542422545382541362538312545362541382541312545352539442539372e706e67)

- Spring Core： 基础模块， Spring其它的功能都依赖于这个类库， 主要提供IOC依赖注入功能
- Spring Aspects： 该模块与AspectJ的集成提供支持
- Spring AOP： 提供了面向切面的编程实现
- Spring JDBC： JAVA数据库连接
- Spring JMS： JAVA消息服务
- Spring ORM： 用于支持Hibernate等ORM工具
- Spring Web：为了创建Web应用程序提供支持
- Spring Test：提供了对Junit和TestNG测试支持

<!--more-->

# Spring IoC（工厂模式）

IoC 是一种设计思想， 将原本在程序中手动创建对象的控制权， 交由Spring框架来管理。

IoC容器是Spring用来实现IoC的载体， IoC容器实际上就是个Map(K, V)， 存放各种对象。

IoC容器就像是一个工厂一样，当我们需要创建一个对象的时候， 只需要配置好配置文件或注解即可， 完全不用考虑对象是怎么被创建出来的。

**为了更好地去了解IoC， 此处我需要补充几个知识点**

依赖倒置原则： 把原本的高层建筑依赖底层建筑倒置过来， 变成底层建筑依赖高层建筑， 高层建筑需要什么， 底层建筑便去实现这样的需求， 高层并不需要管底层是怎么实现的， 这样就不会出现牵一发而动全身的情况。

DI(Dependecy Inject,依赖注入)是实现控制反转的一种设计模式，依赖注入就是将实例变量传入到一个对象中去

控制反转就是依赖倒置原则的一种代码设计思路， 具体方法是依赖注入。

![](https://pic1.zhimg.com/80/v2-ee924f8693cff51785ad6637ac5b21c1_hd.jpg)

![](https://pic1.zhimg.com/80/v2-c920a0540ce0651003a5326f6ef9891d_hd.jpg)

![](https://pic3.zhimg.com/80/v2-24a96669241e81439c636e83976ba152_hd.jpg)

[Spring IoC有什么好处](https://www.zhihu.com/question/23277575/answer/169698662)

# AOP
AOP（面向切面编程）能够将那些与业务代码无关， 却为业务模块所共同调用的逻辑或责任（如事务处理、日志处理、权限控制等）封装起来， 以便于减少系统的重复代码， 降低模块间的耦合度， 并有利于未来的可拓展性和可维护性。

Spring AOP基于动态代理， 如果要代理的对象， 实现了某个接口， 那么Spring AOP就会使用JDK Proxy， 去创建代理对象， 没有接口的话，就无法使用JDK Proxy去代理， 此时便要使用Cglib

![](https://camo.githubusercontent.com/c54f144f88f5e38e1adccfaebf3388eaeb45a333/68747470733a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f323031392d362f537072696e67414f5050726f636573732e6a7067)

#### Spring AOP 与 AspectJ AOP
前者集成了后者， 前者比后者简单， 后者比前者强大

Spring AOP是运行时增强， AspectJ AOP是编译时增强

# Spring 中的事务管理
- 编程式事务：在代码中硬编码（不推荐使用）
- 声明式事务：在配置文件中配置（推荐使用）

**声明式事务又分为两种**
- 基于XML的声明式事务
- 基于注解的声明式事务

#### Spring 事务中的隔离级别
- **TransactionDefinition.ISOLATION_DEFAULT**: 使用后端数据库默认的隔离级别，Mysql 默认采用的 REPEATABLE_READ隔离级别 Oracle 默认采用的 READ_COMMITTED隔离级别.
- **TransactionDefinition.ISOLATION_READ_UNCOMMITTED**: 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
- **TransactionDefinition.ISOLATION_READ_COMMITTED**: 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
- **TransactionDefinition.ISOLATION_REPEATABLE_READ**: 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **TransactionDefinition.ISOLATION_SERIALIZABLE:** 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

# Spring 设计模式
- 工厂设计模式 : Spring使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。
- 代理设计模式 : Spring AOP 功能的实现。
- 单例设计模式 : Spring 中的 Bean 默认都是单例的。
- 模板方法模式 : Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- 包装器设计模式 : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- 观察者模式: Spring 事件驱动模型就是观察者模式很经典的一个应用。
- 适配器模式 :Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller。

[面试官:“谈谈Spring中都用到了那些设计模式?”。](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485303&idx=1&sn=9e4626a1e3f001f9b0d84a6fa0cff04a&chksm=cea248bcf9d5c1aaf48b67cc52bac74eb29d6037848d6cf213b0e5466f2d1fda970db700ba41&token=255050878&lang=zh_CN#rd)

# 参考
[JavaGuide](https://github.com/Snailclimb/JavaGuide/blob/master/docs/system-design/framework/spring/SpringInterviewQuestions.md#spring-aop-%E5%92%8C-aspectj-aop-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)
