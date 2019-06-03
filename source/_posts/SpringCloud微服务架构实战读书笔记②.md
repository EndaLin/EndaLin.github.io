---
title: SpringCloud微服务架构实战读书笔记②
copyright: true
date: 2019-06-03 15:45:27
tags: JAVA
---

# 使用Feign实现声明式REST调用

#### 为服务消费者整合Feign
- 添加依赖
```java
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```
- 创建一个Feign接口，并添加@FeignClient注解
```JAVA
@FeignClient(name="applicationName")
public interface UserFeignClient {
  @RequestMapping(value = "/{id}")
  public User findById(@PathVatiable("id")Long id);
}
```
- **@FeignClient注解**是任意一个服务提供方的名称，用以创建Ribbon负载均衡器。

#### 自定义Feign配置
修改接口
```java
@FeignClient(name="applicationName"， configuration = FeignConfiguration.class)
```
有一点值得注意的是，本例中的FeignConfiguration类不能包括在主程序上下文的@ComponentScan中，否则该类中的配置信息就会被所有的@FeignClient共享， 如果只想定义某个FeignClient客户端的配置，该点需要特别注意。此处可以配置@ComponentScan不扫描配置类的所在包


#### 手动创建Feign

#### Feign对继承的支持

#### Feign对压缩的支持

#### Feign的日志

#### 使用Feign构造多参数请求
Spring Cloud为Feign添加了Spring MVC的注解支持。
- 例子一
```JAVA
@FeignClient(name="applicationName")
public interface UserFeignClient {
  @GetMapping(value = "/{id}")
  public User findById(@RequestParam Map<String, Object> map);
}
```
- 例子二
```JAVA
@FeignClient(name="applicationName")
public interface UserFeignClient {
  @PostMapping(value = "/{id}")
  public User findById(User user);
}
```
