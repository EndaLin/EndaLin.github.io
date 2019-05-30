---
title: 利用Python进行数据分析－Pandas入门
copyright: true
date: 2019-05-30 12:59:56
tags: Python
---
>本文出自<利用Python进行数据分析 第二版> 侵删
# Pandas
Pandas是一个非常巨大的库，采用了大量Numpy的编程风格，基于Numpy构建，含有使数据清洗和数据分析工作变得更加简单的工具．

# Pandas的数据结构
Pandas两个主要的数据结构：Series和DataFrame

<!--more-->

#### Series
Series是由一组数据以及一组索引组成，其数据类型包括各种NumPy数据类型．可用Series()函数来定义
```
In [62]: obj = pd.Series([1,2,3])

In [63]: obj
Out[63]:
0    1
1    2
2    3
dtype: int64
```
在没有指定索引形式的情况下，Pandas自动为数据指定了(０－Ｎ-1)的索引结构，我们可用通过index和values属性来查看相关的信息
```
In [64]: obj.values
Out[64]: array([1, 2, 3])

In [65]: obj.index
Out[65]: RangeIndex(start=0, stop=3, step=1)
```
除此之外，我们在创建Series的时候，还可以指定索引的形式，这一次和有序字典相似，所以，其实我们可以直接将一个字典使用Series函数转换成Series，同时我们还可以通过索引来访问其中的value,此处和NumPy的数据匹配方面是十分相似
```
In [67]: obj2 = pd.Series([90,80,60],index = ['a','b','c'])

In [68]: obj2
Out[68]:
a    90
b    80
c    60
dtype: int64

In [70]: obj2.index　　　#此时访问index属性便会显示具体的索引值
Out[70]: Index(['a', 'b', 'c'], dtype='object')

In [69]: obj2['a']
Out[69]: 90
```
与NumPy相比，Series有一个杰出的优点，Series可以通过索引来取一组数值，还可以借此来检索是否存在对应的数据，若有，则返回实际的value，若无，则返回NAN
备注：Pandas的isnull和notnull函数可用于检测缺失数据，返回boolean值
```
In [74]: obj2[['c','a']]
Out[74]:
c    60
a    90
dtype: int64

In [77]: obj2[['c','g']]
Out[77]:
c    60.0
g     NaN
dtype: float64
```
同时，Series还可以使用NumPy的函数或者NumPy所拥有的一些运算
```
In [73]: obj2[obj2>80]
Out[73]:
a    90
dtype: int64

In [75]: obj2*2
Out[75]:
a    180
b    160
c    120
dtype: int64

In [76]: np.exp(obj2)
Out[76]:
a    1.220403e+39
b    5.540622e+34
c    1.142007e+26
dtype: float64
```
对于绝大多数的功能而言,Series最重要的一个跟你是,根据运算的索引标签来自动对齐数据
 ```
In [35]: obj3
Out[35]:
Ohio      35000
Oregon    16000
Texas     71000
Utah       5000
dtype: int64

In [36]: obj4
Out[36]:
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
dtype: float64

In [37]: obj3 + obj4
Out[37]:
California         NaN
Ohio           70000.0
Oregon         32000.0
Texas         142000.0
Utah               NaN
dtype: float64
```
Series对象本身及其索引都有一个name属性,该属性跟pandas其它的关键功能关系非常密切
```
In [38]: obj4.name = 'population'

In [39]: obj4.index.name = 'state'

In [40]: obj4
Out[40]:
state
California        NaN
Ohio          35000.0
Oregon        16000.0
Texas         71000.0
Name: population, dtype: float64
```
Series的索引可以通过赋值的方式就地修改：
```
In [41]: obj
Out[41]:
0    4
1    7
2   -5
3    3
dtype: int64

In [42]: obj.index = ['Bob', 'Steve', 'Jeff', 'Ryan']

In [43]: obj
Out[43]:
Bob      4
Steve    7
Jeff    -5
Ryan     3
dtype: int64
```
***
# DataFrame
DataFrame是一个表格型的数据结构,以二维结构保存数组.
构建DataFrame最常用的方法是闯入一个等长列表或者NumPy数组构成的字典,构建出来的DataFrame会被自动加上索引
备注:head函数可以使得DataFrame只显示前五行,同时我们还可以像在Series一样,给DataFrame指定索引值
```
In [84]: data = {'a':[1,2,3],'b':[4,5,6],'c':[7,8,9]}

In [85]: frame = pd.DataFrame(data)

In [86]: frame
Out[86]:
   a  b  c
0  1  4  7
1  2  5  8
2  3  6  9
```
下面我们再来看一个典型的例子
我们在构建的时候可以指定列序列,可以和数据源对不上,顺序以传入数据为准,但是,传入的index的长度必须要和数据源的列长度相等.
```
In [90]: frame2 = pd.DataFrame(data,columns=['a','c','d'],index = ['one','two','three'])

In [91]: frame2
Out[91]:
       a  c    d
one    1  7  NaN
two    2  8  NaN
three  3  9  NaN

In [92]: frame2.columns
Out[92]: Index(['a', 'c', 'd'], dtype='object')
```
可以通过列名来得到一个Series,有两种方法
```
In [93]: frame2['a']  #第一种
Out[93]:
one      1
two      2
three    3
Name: a, dtype: int64

In [95]: frame2.a  #第二种
Out[95]:
one      1
two      2
three    3
Name: a, dtype: int64

```
同理,可以通过行名来得到一个Series
```
In [99]: frame2.loc['one']
Out[99]:
a      1
c      7
d    NaN
Name: one, dtype: object
```
