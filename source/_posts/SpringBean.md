---
title: SpringBean
copyright: true
date: 2019-06-08 15:06:40
tags: Spring
---

# 前言
在Spring中，那些组成应用程序的主体以及那些由Spring IoC 容器锁管理的对象，被称之为**bean**。

简单来讲，bean就是由IoC容器初始化、装配及管理的对象。

Spring中的bean默认是单例的，Spring的单例基于JVM，每个JVM内只有一个实例。

在大多数情况下，单例子bean都是很理想的方案，除了使用一些需要保持一些状态的bean.

<!--more-->

# bean的作用域
![作用域](https://camo.githubusercontent.com/adf4379800711a819fda44c2f478c27469ee5a86/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d392d31372f313138383335322e6a7067)

#### 配置和注解
```xml
<bean id="ServiceImpl" class="cn.csdn.service.ServiceImpl" scope="singleton">
```
```java
@Service
@Scope("singleton")
public class ServiceImpl{
}
```

# bean的生命周期
#### initialization and destroy
Spring 框架提供了很多方法让我们在Spring Bean生命周期中执行initialization和pre-destroy方法.
- 使用@PostConstruct和@PreDestroy注解
```java
public class GiraffeService {
    @PostConstruct
    public void initPostConstruct(){
        System.out.println("执行PostConstruct注解标注的方法");
    }
    @PreDestroy
    public void preDestroy(){
        System.out.println("执行preDestroy注解标注的方法");
    }
}
```
- 通过bean的配置文件中指定init-method和destroy-method方法
```xml
<bean name="giraffeService" class="com.giraffe.spring.service.GiraffeService"
  init-method="initMethod" destroy-method="destroyMethod">
</bean>
```
```java
public class GiraffeService {
    //通过<bean>的destroy-method属性指定的销毁方法
    public void destroyMethod() throws Exception {
        System.out.println("执行配置的destroy-method");
    }
    //通过<bean>的init-method属性指定的初始化方法
    public void initMethod() throws Exception {
        System.out.println("执行配置的init-method");
    }
}
```

#### Aware接口
- ApplicationContextAware: 获得ApplicationContext对象,可以用来获取所有Bean definition的名字。
- BeanFactoryAware:获得BeanFactory对象，可以用来检测Bean的作用域。
- BeanNameAware:获得Bean在配置文件中定义的名字。
- ResourceLoaderAware:获得ResourceLoader对象，可以获得classpath中某个文件。
- ServletContextAware:在一个MVC应用中可以获取ServletContext对象，可以读取context中的参数。
- ServletConfigAware： 在一个MVC应用中可以获取ServletConfig对象，可以读取config中的参数。

#### 总结
- Bean容器找到配置文件中 Spring Bean 的定义。
- Bean容器利用Java Reflection API创建一个Bean的实例。
- 如果涉及到一些属性值 利用set方法设置一些属性值。
- 如果Bean实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字。
- 如果Bean实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 如果Bean实现了BeanFactoryAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 与上面的类似，如果实现了其他Aware接口，就调用相应的方法。
- 如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执-行postProcessBeforeInitialization()方法
- 如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。
- 如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。
- 如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执 行postProcessAfterInitialization()方法
- 当要销毁Bean的时候，如果Bean实现了DisposableBean接口，执行destroy()方法。
- 当要销毁Bean的时候，如果Bean在配置文件中的定义包含destroy-method属性，执行指定的方法。
![SpringBean生命周期](https://camo.githubusercontent.com/d275f148f8928ccc284180731f90991891be1a35/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d392d31372f34383337363237322e6a7067)


# 参考
[JavaGuide-Spring Bean](https://github.com/Snailclimb/JavaGuide/blob/master/docs/system-design/framework/spring/SpringBean.md)
