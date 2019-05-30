---
title: String、StringBuilder和StringBuffer
copyright: true
date: 2019-05-30 19:13:33
tags: JAVA
---

# String
```java
public final class String implements Serializable, Comparable<String>, CharSequence {
    private final char[] value;
    private int hash;
    private static final long serialVersionUID = -6849794470754667710L;
    private static final ObjectStreamField[] serialPersistentFields = new ObjectStreamField[0];
    public static final Comparator<String> CASE_INSENSITIVE_ORDER = new String.CaseInsensitiveComparator();

    public String() {
        this.value = "".value;
    }

    public String(String var1) {
        this.value = var1.value;
        this.hash = var1.hash;
    }

    public String(char[] var1) {
        this.value = Arrays.copyOf(var1, var1.length);
    }
```
从String 的源码中，我们不难发现， String存储的字符串， 对象是final类型的， 不可变， 线程安全。

# StringBuffer and StringBuilder

StringBuffer和StringBuilder都继承了AbstractStringBuilder并且使用的基本都是父类的方法，区别在于前程是线程安全的，后者是线程不安全的，前者比后者快。
```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    char[] value;
    int count;
    private static final int MAX_ARRAY_SIZE = 2147483639;

    AbstractStringBuilder() {
    }

    AbstractStringBuilder(int var1) {
        this.value = new char[var1];
    }

```

#### 性能
每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象。StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

#### 总结
- 操作少量的数据: 适用String
- 单线程操作字符串缓冲区下操作大量数据: 适用StringBuilder
- 多线程操作字符串缓冲区下操作大量数据: 适用StringBuffer
