---
title: Java自定义Annotation注解
copyright: true
date: 2019-07-10 09:35:39
tags: JAVA
---

# 元注解

元注解的作用在于： 负责注解其它注解。

- **@Target**
- **@Retention**
- **@Documented**
- **@Inherited**

## @Target

**@Target** 说明了Annotation 所修饰的对象的范围， 使用枚举类**ElementType** 来指定。

```JAVA
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE,

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

<!--more-->


## @Retention
**@Retention** ： 用于描述注解的生命周期， 即被描述的注解在什么范围内有效。

取值范围有(在枚举类RetentionPolicy 中有说明)：
- SOURCE：在源文件中有效（即在源文件中保留）
- CLASS：在class 文件中有效（即在CLASS 文件中保留）
- RUNTIME：在运行时有效（即在运行时保留）

```JAVA
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     */
    SOURCE,

    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     */
    CLASS,

    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     *
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME
}
```

## @Documented
**@Documented** 用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。Documented是一个标记注解，没有成员。

## @Inherited
**@Inherited** 是一个标记注解， 阐述了某个被标注的类型是被继承的，


# 参考文档
- [深入理解Java：注解（Annotation）自定义注解入门](https://www.cnblogs.com/peida/archive/2013/04/24/3036689.html)
- [秒懂，Java 注解 （Annotation）你可以这样学](https://blog.csdn.net/briblue/article/details/73824058)
- [深入了解Java 发射机制](https://www.sczyh30.com/posts/Java/java-reflection-1/)
