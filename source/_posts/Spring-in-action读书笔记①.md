---
title: Spring_in_action读书笔记①
copyright: true
date: 2019-07-12 12:39:58
tags: Spring
---


# bean

## Spring 应用上下文

Spring 自带了多种类型的应用上下文：
- AnnotationConfigApplicationContext： 从一个或者多个基于Java 的配置类中加载Spring 应用上下文。
- AnnotationConfigWebApplicationContext：从一个或者多个基于Java 的配置类中加载Spring Web 应用上下文。
- ClassPathXmlApplicationContext：从类路径下的一个或者多个XML 配置文件中加载上下文定义， 把应用上下文的定义文件作为类资源。
- FileSystemXmlApplicationContext：从文件系统下的一个或者多个Xml 配置文件中加载上下文定义。
- XmlWebApplicationContext：从Web应用下的一个或者多个Xml 文件中加载上下文定义。

Spring Boot 默认的类路径
```Java
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = {  
        "classpath:/META-INF/resources/",
        "classpath:/resources/",  
        "classpath:/static/",
        "classpath:/public/" };  
```

<!--more-->

## bean 的生命周期

![image.png](https://upload-images.jianshu.io/upload_images/13918038-0ac90624b41ec2e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## @ComponentScan

这个注解能够在Spring 中启用组件扫描， 如果没有其他配置， @ComponentScan 默认会扫描当前包及其子包下所有的组件。
