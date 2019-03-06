---
title: 探究Spring Boot 核心技术
date: 2019-03-06 20:59:45
tags: Spring Boot
---
# 探究Spring Boot

## POM 文件
##### 1.父项目
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

他的父项目是
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.1.1.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
这个父项目是真正管理Spring Boot应用里面所有依赖版本,
因为里面有一个dependency version, 为各种jar包定义了默认的版本号
```
Spring Boot的版本仲裁中心;
以后我们导入依赖默认是不需要写版本的, (在没有dependencies里面管理的依赖自然要声明版本号)

##### 2.起步依赖
- Spring-boot-starter 场景启动器
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
> - 举个例子,***spring-boot-starter***-web,帮我们导入web应用开发所需要的所有依赖
> - Spring boot 把所有的功能场景都抽取出来,做成一个个starters(启动器), 只需要在项目里面导入这些starters, 所有相关的依赖都会导入进来
> - 一般需要什么功能就导入什么场景的启动器

## 主程序类,主入口类
```java
/**
 * SpringBootApplication 来标注一个主程序类, 说明这是一个Spring Boot 应用
 */
@SpringBootApplication
public class SpringbootSampleApplication {

    public static void main(String[] args) {

        SpringApplication.run(SpringbootSampleApplication.class, args);
    }
}
```
- @SpringBootApplication: Spring Boot应用标注在某个类上来说明这个类是Spring Boot的主配置类, Spring Boot就应该运行这个类的main方法来启动Spring Boot应用

```java
/**
*SpringBootApplication 部分源码
*/
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```
- @SpringBootConfiguration : Spring Boot的配置类
- @EnableAutoConfiguration: 开启自动配置功能
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```
- @AutoConfiguraPackage:**将主配置类(@SpringBootApplication标注的类)的所在包以及下面所有子包里面的所有组件都扫描到Spring容器中, 这一点要特别注意!!!**
> - 举个例子
> - ![SpringBootApplication](/image/SpringBootApplication.png)

## Spring Boot 目录结构
![目录结构](/image/SpringBoot.png)
- 这里主要说resources文件夹下面的结构
> - static: 保存所以静态资源, 如js css images等等
> - templates: 保存所以模板页面, Spring Boot默认jar包使用嵌入式tomcat, 默认不支持JSP页面, 可以使用模板引擎(freemarker, 或者Spring Boot推荐的thymeleaf)
> - application.properties : Spring Boot 的配置文件, 可以修改一些默认配置

## Spring Boot配置文件
#### 1.配置文件
Spring Boot 使用一个全局的配置文件, 配置文件名是固定的
- application.properties
- application.yml **(推荐使用)**

配置文件的作用在于: 修改Spring Boot自动配置的默认值

#### 2.YAML基本语法
- 键值对的形式, 以空格来控制层级关系
- k:(空格)v
- 空格不能省, 大小写敏感
- 推荐使用YMAL来做配置文件, 例子
```yaml
server:
  port: 8081
```
- YAML value的写法值得需要注意的地方
> - 字符串默认不用加上单引号或者双引号
> - - 如果加上双引号, 则不会转义字符串里面的特殊字符, 如 "wt \n wt" 输出的是 "wt \n wt"
> - - 如果加上单引号, 则会转义字符串里面的特殊字符, 如 "wt \n wt" 输出的是 "wt 换行 wt"
> - 数组, 使用-值表示数组中的一个元素
> ```
> pets:
>  - cats
>  - dog
>  - pig
>```

#### 3. YAML配置文件值注入
- 此处举例子进行说明
- 首先编写YAML文件

```yml
person:
  name: wt
  age: 22
  maps: {k1: v1, k2: v2}
  dog:
    name: my_dog
    age: 2
```

- 导入依赖

```xml
<!--导入配置文件处理器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

- JAVABean

```java

/**
 * 将配置文件中配置的每一个属性的值,映射到这个组件中
 * @ConfigurationProperties 告诉Spring Boot 将本类中的所有属性和配置文件中的相关配置进行绑定
 * prefix = "person" 配置文件中哪一个属性进行绑定, 以person开头的值
 *
 * 只有这个组件是容器中的组件,才能使用容器提供的@ConfigurationProperties功能
 */

@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String name;
    private Integer age;
    private Map<String, Object> maps;
    private Dog dog;

    ---
}
```
- JAVABean

```java
public class Dog {
    private String name;
    private Integer age;

    ---
}
```

- 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootSampleApplicationTests {

    @Autowired
    Person person;

    @Test
    public void contextLoads() {
        System.out.println(person);
    }

}

输出 Person{name='wt', age=22, maps={k1=v1, k2=v2}, dog=Dog{name='my_dog', age=2}}
```
#### 4. @Value 和 @ConfigurationProperties的区别
 | @ConfigurationProperties | @Value
:----------- | :-----------: | -----------:
功能         | 批量注入配置属性文件的属性        | 一个个指定
松散绑定(松散语法)         | 支持        | 不支持
SpringEL        | 不支持        | 支持
JR303数据校验         | 支持        | 不支持
复杂类型         | 支持        | 不支持

如果说, 我们只是需要在某个业务逻辑中获取一下配置文件的某个值, 则使用@Value, 可读取YAML文件
```java
@RestController
public class HelloController {

    @Value("${person.name}")
    private String name;

    @RequestMapping("/sayHello")
    public String sayHello() {
        return "hello " + name;
    }
}
```
如果需要与一个JAVABean进行映射,则使用@ConfigurationProperties来批量注入

#### 5.介绍两个有用且相关的注解, @PropertySource 和 @ImportResource
首先先介绍@**PropertySource**, 此处有一点值得注意的是,官网里面有这样一句话:YAML files cannot be loaded by using the **@PropertySource** annotation. So, in the case that you need to load values that way, you need to use a properties file. 意思就是说, @PropertySource只能用于.properties文件而不能用于YMAL文件, 所以, 此处我们还是举个例子来进行说明.
- 创建person.properties 文件
```xml
person.name=wt
person.age=22
person.maps.k1=v1
person.maps.k2=v2
person.dog.name=my_dog
person.dog.age=2
```
- 修改Person类, 添加@PropertySource(value = {"classpath:person.properties"})
```java
/**
 * 将配置文件中配置的每一个属性的值,映射到这个组件中
 * @ConfigurationProperties 告诉Spring Boot 将本类中的所有属性和配置文件中的相关配置进行绑定
 * prefix = "person" 配置文件中哪一个属性进行绑定 默认从全局配置获取值, 以person开头的值
 *
 * 只有这个组件是容器中的组件,才能使用容器提供的@ConfigurationProperties功能
 */
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String name;
    private Integer age;
    private Map<String, Object> maps;
    private Dog dog;

    ---
```

接下来, 介绍@**ImportResource**, 它的作用是引入Spring 配置文件, Spring Boot里面没有Spring的配置文件, 我们自己写的配置文件是不能被自动识别的, 此时, 便要使用@**ImportResource**, 放在一个配置类上
举个例子
- 创建一个XML文件
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="helloService" class="com.example.springbootsample.service.HelloService"></bean>
</beans>
```

- 添加@ImportResource到主配置类中
```java
/**
 * SpringBootApplication 来标注一个主程序类, 说明这是一个Spring Boot 应用
 */
@ImportResource(locations = {"classpath:beans.xml"})
@SpringBootApplication
public class SpringbootSampleApplication {

    public static void main(String[] args) {

        SpringApplication.run(SpringbootSampleApplication.class, args);
    }
}
```
- 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootSampleApplicationTests {

    @Autowired
    Person person;

    @Autowired
    ApplicationContext ioc;

    @Test
    public void testHelloService() {
        boolean b = ioc.containsBean("helloService");
        System.out.println(b);
    }

}

输出true
```

然而, Spring Boot 不推荐使用XML, 而是使用**全注解的方式**来为容器添加组件
- 创建一个配置类
- 使用@Bean来给容器添加组件

```java
package com.example.springbootsample.config;

import com.example.springbootsample.service.HelloService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Configuration 指明当前类是一个配置类, 来替代之前的Spring配置文件
 *
 * 在Spring配置文件中, 这是用<bean></bean>标签来添加组件的
 *
 */
@Configuration
public class MyAppConfiguration {

    // 将方法的返回值添加到容器中, 容器中默认的组件id就是这个方法名helloService01
    @Bean
    public HelloService helloService01() {
        System.out.println("添加组件");
        return new HelloService();
    }
}
```

#### 6.配置文件占位符
- 随机数
```java
${random.value} ${random.int} ${random.long} ${random.int[1024, 65536]}
```
- 使用占位符获取之前配置的值, 如果没有, 则使用 **:** 指定默认值
```java
person.name=wt${random.uuid}
person.age=22
person.maps.k1=v1
person.maps.k2=${person.hello:hello}
person.dog.name=my_dog
person.dog.age=2
```

#### 7.Profile
Profile 是 Spring 对不同环境提供不同配置功能的支持, 可以通过激活或者指定参数等方式快速切换环境, 默认使用application.properties
- 多Profile文件形式:
> - 格式: application-{profile}.properties/yml
> - 如: application-dev.properties
- 激活指定的profile
> - 在默认的配置文件中, 指定**spring.profiles.ative=dev,** 则会使用application-dev.properties
> - 命令行的方式: **--spring.profiles.ative=dev**, 可以在java -jar 命令后面写入, 可以在idea tomcat那里配置
> - 虚拟机参数-Dspring.profile.active=dev
- YAML可以用文档块来指定不同的环境, 如

```yml
server:
  port: 8081
spring:
  profiles:
    active: dev  #激活dev环境, 如果不指明, 则使用默认的8081端口
---
server:
  port: 8082
spring:
  profiles: dev
---
server:
  port: 8083
spring:
  profiles: prod
```

#### 8.配置文件的加载位置
Spring Boot 启动会扫描以下位置的application.properties 或 application.yml文件作为spring boot 的配置文件, 优先级从高到底, 高优先级的内容会覆盖低优先级内容
> - file:./config/
> - file:./
> - classpath:/config/
> - classpath:/

值得注意的是, Spring Boot会从这四个位置全部加载主配置文件, 且互补配置, 即, 高优先级有的, 使用高优先级, 高优先级没有的而低优先级有的, 则使用低优先级


#### 9.外部配置文件的加载位置
此处内容较多, 建议参考 [官方文档中的Externalized Configuration](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config)

#### 10.配置原理
配置文件的配置内容, 参考[官方文档附录Common application properties](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#common-application-properties)

#### 11、@Conditional派生注解（Spring注解版原生的@Conditional作用）

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional扩展注解                | 作用（判断是否满足当前指定条件）               |
| ------------------------------- | ------------------------------ |
| @ConditionalOnJava              | 系统的java版本是否符合要求                |
| @ConditionalOnBean              | 容器中存在指定Bean；                   |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                  |
| @ConditionalOnExpression        | 满足SpEL表达式指定                    |
| @ConditionalOnClass             | 系统中有指定的类                       |
| @ConditionalOnMissingClass      | 系统中没有指定的类                      |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                 |
| @ConditionalOnWebApplication    | 当前是web环境                       |
| @ConditionalOnNotWebApplication | 当前不是web环境                      |
| @ConditionalOnJndi              | JNDI存在指定项                      |

#### 12, 打印自动配置类启用报告
在配置文件中加上debug=true属性
控制台便会打印相关的信息
```java
============================
CONDITIONS EVALUATION REPORT
============================


Positive matches: (自动配置类启用的)
-----------------

   AopAutoConfiguration matched:
      - @ConditionalOnClass found required classes 'org.springframework.context.annotation.EnableAspectJAutoProxy', 'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice', 'org.aspectj.weaver.AnnotatedElement' (OnClassCondition)
      - @ConditionalOnProperty (spring.aop.auto=true) matched (OnPropertyCondition)

   AopAutoConfiguration.CglibAutoProxyConfiguration matched:
      - @ConditionalOnProperty (spring.aop.proxy-target-class=true) matched (OnPropertyCondition)

    ---

Negative matches: (自动配置类未启用的, 没有匹配成功)
----------------

   ActiveMQAutoConfiguration:
      Did not match:
      - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory' (OnClassCondition)

  AopAutoConfiguration.JdkDynamicAutoProxyConfiguration:
      Did not match:
      - @ConditionalOnProperty (spring.aop.proxy-target-class=false) did not find property 'proxy-target-class' (OnPropertyCondition)

      ---
```
