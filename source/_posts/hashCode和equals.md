---
title: hashCode和equals
copyright: true
date: 2019-05-31 10:13:46
tags: JAVA
---
> 问题缘由：重写equals时必须要重写hashCode方法

# 简介
hashCode()的作用是获取哈希码， 也称为散列码，它实际上就是一个int整数， 这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode定义在JDK的Object中， 这就意味者所有的类都含有hashCode方法

散列表存储的是键值对， 它的特点是， 能够根据键快速检索出对应的值， 其中就利用到了散列码。

哈希码的作用是获取对象在哈希表中的索引位置。


#### HashSet的插入机制
HashSet不允许重复的插入， 在每一次插入时， 会根据hashCode来判断对象的插入位置， 同时会与已经存在的hashcode作比较， 如果没有相符的hashCode， 则没有重复的对象， 如果有重复的hashCode, 则会使用equals来判断值是否相等， 若相等则不插入， 若不相等则重新散列到其它位置， 这样可以大大减少equals的次数， 大大提高执行速度。


#### 重写equals时必须要重写hashCode方法

因为在使用equals之前，首先是使用hashcode去判断对象的索引位置， hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）



#### demo

```java
import java.util.*;
import java.lang.Comparable;

public class ConflictHashCodeTest2{

    public static void main(String[] args) {
        // 新建Person对象，
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);
        Person p4 = new Person("EEE", 100);

        // 新建HashSet对象
        HashSet set = new HashSet();
        set.add(p1);
        set.add(p2);
        set.add(p3);

        // 比较p1 和 p2， 并打印它们的hashCode()
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        // 比较p1 和 p4， 并打印它们的hashCode()
        System.out.printf("p1.equals(p4) : %s; p1(%d) p4(%d)\n", p1.equals(p4), p1.hashCode(), p4.hashCode());
        // 打印set
        System.out.printf("set:%s\n", set);
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }

        /**
         * @desc重写hashCode
         */  
        @Override
        public int hashCode(){  
            int nameHash =  name.toUpperCase().hashCode();
            return nameHash ^ age;
        }

        /**
         * @desc 覆盖equals方法
         */  
        @Override
        public boolean equals(Object obj){  
            if(obj == null){  
                return false;  
            }  

            //如果是同一个对象返回true，反之返回false  
            if(this == obj){  
                return true;  
            }  

            //判断是否类型相同  
            if(this.getClass() != obj.getClass()){  
                return false;  
            }  

            Person person = (Person)obj;  
            return name.equals(person.name) && age==person.age;  
        }
    }
}
```
