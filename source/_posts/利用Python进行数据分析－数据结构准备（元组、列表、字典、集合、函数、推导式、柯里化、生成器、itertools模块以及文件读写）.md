---
title: 利用Python进行数据分析－数据结构准备（元组、列表、字典、集合、函数、推导式、柯里化、生成器、itertools模块以及文件读写）
copyright: true
date: 2019-05-30 12:58:45
tags: Python
---
>本文引用《利用Python进行数据分析·第2版》
# 元组tuple

元组是一个固定长度而且不可以改变的序列对象

#### 定义元组的方法:

#### (1) 最简单的方法:
```
In [23]: top = 1,2,3

In [24]: top

Out[24]: (1, 2, 3)
```

<!--more-->

#### (2) 复杂元组的定义:
```

In [25]: top = (1,2,3),(4,5)

In [26]: top

Out[26]: ((1, 2, 3), (4, 5))
```

#### 常用方法:

#### tuple : 将任意序列或者迭代器转换成元组
```

In [27]: tuple([1,2,3])

Out[27]: (1, 2, 3)

In [28]: tuple("wt")

Out[28]: ('w', 't')
```
#### count：统计频率
```
In [50]: a = 1,2,2,3

In [51]: a.count(2)

Out[51]: 2
```

#### 访问 : 可以直接通过下标来访问
```
In [29]: top[0]

Out[29]: (1, 2, 3)
```
#### 修改:元组的对象一旦定义便不可以修改，除非其中存储的对象是可变对象，例如：
```
In [30]: top = 1, [1,2,3],'a'

In [31]: top[1].append('wt')

In [32]: top

Out[32]: (1, [1, 2, 3, 'wt'], 'a')
```
备注: [1,2,3]　为列表，有序的集合，可以随时添加和删除其中的元素

#### 串联：　可以使用加法运算符把元组串联起来
```
In [32]: top

Out[32]: (1, [1, 2, 3, 'wt'], 'a')

In [33]: top + (4,5,6)　＃第一种情况

Out[33]: (1, [1, 2, 3, 'wt'], 'a', 4, 5, 6)

In [45]: top * 2　　　＃第二种情况

Out[45]: (1, [1, 2, 3, 'wt'], 'a', 1, [1, 2, 3, 'wt'], 'a')
```
#### 拆分：拆分有两种情况，一种是等量拆分，另一种是不等量拆分

#### 等量拆分：
```
In [34]: top = 1,2,3

In [35]: a,b,c = top

In [36]: a

Out[36]: 1
```
#### 不等量拆分，ｐ可以分配不等长度的列表
```
In [42]: a,*p = top

In [43]: p

Out[43]: [2, 3]
```
#### 变量拆分常用来迭代元组或者列表序列
```
In [46]: seq = (1,2,3),(4,5,6)

In [49]: for a,b,c in seq:

    ...:    print("a = {0}, b = {1}, c = {2}".format(a,b,c))

    ...:   

a = 1, b = 2, c = 3

a = 4, b = 5, c = 6

* * *
```
# 列表list

与元组相比，列表的长度和内容都是可变的

#### 定义的方法：

#### １．直接定义
```
In [52]: a_list = [1,2,3,'wt']

In [53]: a_list

Out[53]: [1, 2, 3, 'wt']
```
#### ２．使用list函数，list函数在数据处理中常用来实体化迭代器和生成器
```
In [58]: b_list = range(2,10)

In [59]: list(b_list)

Out[59]: [2, 3, 4, 5, 6, 7, 8, 9]
```
#### 添加和删除元素：

#### １．使用append在列表末尾添加元素
```
In [60]: a_list.append('wt')

In [61]: a_list

Out[61]: [1, 2, 3, 'wt', 'wt']
```
#### ２．使用insert在指定位置上添加元素
```
In [63]: b_list = list(b_list)

In [64]: b_list.insert(1,'wt')

In [65]: b_list

Out[65]: [2, 'wt', 3, 4, 5, 6, 7, 8, 9]
```
#### ３．使用pop去除指定的元素
```
In [66]: b_list.pop(2)

Out[66]: 3

In [67]: b_list

Out[67]: [2, 'wt', 4, 5, 6, 7, 8, 9]
```
#### ４．使用remove去除某个匹配值，优先去除找到的第一个
```
In [69]: b_list = ['wt',1,2,'wt']

In [70]: b_list.remove('wt')

In [71]: b_list

Out[71]: [1, 2, 'wt']
```
#### 常用方法：

#### １．使用in去检查列表里面是否含有某个值（not ni）
```
In [72]: 'wt' in b_list

Out[72]: True
```
#### 2.串联和组合列表

(1)　使用 + 号将两个列表串联起来
```
In [73]: [1,2,3] + [4,5,6]

Out[73]: [1, 2, 3, 4, 5, 6]
```
(2)　使用extend方法追加多个元素（添加的新对象为列表类型，与append不同的是，append添加的是单个元素）
```
In [76]: b_list.extend(['wt','wt'])

In [77]: b_list

Out[77]: [1, 2, 'wt', 'wt', 'wt']
```
区别：使用 + 号串联对象的开销比较大，因为要新建一个列表，如果对象是一个大列表的时候，使用extend追加元素会比较可取

#### ３．使用sort()函数进行原地排序（不创建新的对象）
```
In [80]: a = [3,2,1]

In [81]: a.sort()

In [82]: a

Out[82]: [1, 2, 3]
```
#### ４．切片

![切片示例图](http://upload-images.jianshu.io/upload_images/13918038-f19646786f5d0b32?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 切片的赋值：（注意要与Numpy的特点区分开来，Numpy切片下产生的是视图，而不是新的对象）
```
In [83]: a = [1,2,3,4,5,6,7,8,9]

In [85]: a[2:3] = ['wt','wt']

In [86]: a

Out[86]: [1, 2, 'wt', 'wt', 4, 5, 6, 7, 8, 9]

In [87]: b = a[2:3]　　＃产生一个新的对象

In [88]: b = [1,1]　　＃赋值

In [89]: b

Out[89]: [1, 1]

In [90]: a

Out[90]: [1, 2, 'wt', 'wt', 4, 5, 6, 7, 8, 9]　　＃a的切片区域，数值没有被改变，说明产生的并非视图

In [91]: a[::2]　　＃隔两个数取一个值，顺序

Out[91]: [1, 'wt', 4, 6, 8]

In [92]: a[::-1]　　＃隔一个数取一个值，倒叙

Out[92]: [9, 8, 7, 6, 5, 4, 'wt', 'wt', 2, 1]
```
#### 序列函数：

#### enumerate函数，可以返回（i,value）元组序列
```
In [93]: for i,value in enumerate(a):　＃测试用例

    ...:    print(i,value)

    ...:   

0 1

1 2

2 wt
```
#### sorted函数从任意序列中返回一个已经拍好序的列表（列表的元素需要为同一类型）：
```
In [96]: sorted(["dscdcsd fsdf"]) 　　#空格为分割符

Out[96]: ['dscdcsd fsdf']
```
#### zip函数可以将多个列别，元组或者其它序列组合成一个新的列表
```
In [98]: seq1 = [1,2,3]

In [99]: seq2 = ['wt','wt']

In [100]: seq3 = zip(seq1,seq2)

In [101]: seq3　　#注意，如需展示列表，需要收到转换list()

Out[101]:报错信息

In [102]: list(seq3)

Out[102]: [(1, 'wt'), (2, 'wt')]

In [104]: list(zip(seq1,seq2,seq4))  #zip可以合并任意长度的列别，元素的个数取决于最短的序列

Out[104]: [(1, 'wt', 4), (2, 'wt', 5)]

In [107]: for i,j in zip(*(zip(seq1,seq2,seq4))):　＃zip还可以用来解压，注意需要在被解压对象之前添加一个＊号

    ...:    print(i,j)

    ...:   

1 2

wt wt

4 5

#### reversed函数可以对一个列表进行倒叙处理

In [109]: list(reversed([1,2,3]))

Out[109]: [3, 2, 1]

* * *
```
# 字典dict

字典是Python重要的数据结构，被我们称之为哈希映射或者关联数组，键和值都是python对象,  其中，值可变但键不可变

#### 创建方法：

常用的创建方法是使用｛｝尖括号：
```
In [110]: dict = {"wt":10,"wt2":20}

In [111]: dict

Out[111]: {'wt': 10, 'wt2': 20}
```
#### 访问、插入和设定：

#### 访问、插入和设定的方式可以像数组一样
```
In [2]: dict['wt3'] = 30   #通过访问键的方式去插入

In [3]: dict

Out[3]: {'wt': 10, 'wt2': 20, 'wt3': 30}

In [5]: dict['wt2']   #通过键去访问值value

Out[5]: 20
```
#### 删除值（同时也会删除键），使用del或者pop方法
```
In [7]: dict

Out[7]: {'wt': 10, 'wt2': 20, 'wt3': 30}

In [8]: del dict['wt3']                                    #使用del方法去删除键为‘wt3’的值

In [9]: dict

Out[9]: {'wt': 10, 'wt2': 20}

In [9]: dict

Out[9]: {'wt': 10, 'wt2': 20}

In [10]: value = dict.pop('wt2')                   #使用pop方法去删除键为‘wt2’的值，并把删除的值赋值给value

In [11]: value

Out[11]: 20

In [12]: dict

Out[12]: {'wt': 10}
```
#### 常用方法：

#### 通过 in 去检查字典中是否含有指定的键
```
In [6]: 'wt2' in dict

Out[6]: True
```
#### 访问字典中所有的keys
```
In [13]: dict.keys()

Out[13]: dict_keys(['wt'])
```
#### 访问字典中所有的values
```
In [14]: dict.values()

Out[14]: dict_values([10])
```
#### 使用update融合字典
```
In [16]: dict.update({"wt2":20,"wt3":30})          #使用update合并字典

In [17]: dict

Out[17]: {'wt': 10, 'wt2': 20, 'wt3': 30}
```
#### 将两个序列组合成字典
```
In [18]: key_list = [1,2,3]

In [19]: value_list = [4,5,6]

In [20]: mapping = {}
#组合的第一种方法
In [21]: for key,value in zip(key_list,value_list):    

    ...:    mapping[key] = value

    ...:   

In [22]: mapping
Out[22]: {1: 4, 2: 5, 3: 6}
#组合的第二种方法
In [23]: list = list(zip(key_list,value_list))
In [24]: list
Out[24]: [(1, 4), (2, 5), (3, 6)]  #此为二元列表，可以使用dict方法来转换成字典
In [31]: mapping2 = dict(list）
In [32]: mapping2
Out[32]: {1: 4, 2: 5, 3: 6}
```
#### 默认值
dict的方法get和pop可以取默认值，并返回
```
#此种代码逻辑十分常见
In [33]: if key in some_dict:
    ...:     value = some_dict[key]
    ...: else:
    ...:     value = default_value
    ...:    
```
常用的方法还有collections, defaultdict, setdefault
***
# 集合set
集合是无序而又没有重复元素的集合，可以看作只有键而没有值的字典

#### 定义方法:
#### 直接使用{}尖括号
```
In [34]: mySet = {1,2,3}

In [35]: mySet
Out[35]: {1, 2, 3}
```
#### 使用方法set
```
In [36]: mySet = set([4,5,6])

In [37]: mySet
Out[37]: {4, 5, 6}
```
#### 常用方法：
#### 合并，取两个集合不重复的元素，然后组合成一个新的集合，可以用union方法或者 | 运算符
```
In [38]: set1 = {1,2,3}

In [39]: set2 = {2,3,4}

In [40]: set1.union(set2)       #第一种方法，使用union
Out[40]: {1, 2, 3, 4}

In [41]: set1 | set2                #第二种方法，使用 | 运算符
Out[41]: {1, 2, 3, 4}
```
#### 交集，取两个集合中重复的集合，然后组合成一个新的集合，可以用intersection 或者 & 运算符
```
In [42]: set1.intersection(set2)   #第一种方法，使用intersection
Out[42]: {2, 3}

In [43]: set1 & set2   # 第二种方法，使用 & 运算符
Out[43]: {2, 3}
```
![集合常用的方法](https://upload-images.jianshu.io/upload_images/13918038-2bff143d79add2a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
所有逻辑集合操作都有另外的原地实现方法，可以直接用结果替代集合的内容。对于大的集合，这么做效率更高。
***
# 列表，集合，字典推导式
#### 列表推导式：
```
[expr for val in collection if condition]
```
#### 集合推导式
```
set_comp = {expr for value in collection if condition}
```
#### 字典推导式
```
dict_comp = {key-expr : value-expr for value in collection if condition}
```
***
# 函数
函数的应用十分广泛，其中，值得注意的是，函数的返回值的形式可以多种多样，例如，返回字典
```
def f():
  a = 5
  b = 6
  c = 7
  return {'a':a,'b':b,'c':c}
```
有时候，我们需要对同一个字符串做一系列的函数操作，此时，最简单的方法是，我们把所有的方法封装成一个列表，然后再遍历执行（多函数模式）
```
def remove_punctuation(value):
    return re.sub('[!#?]', '', value)

clean_ops = [str.strip, remove_punctuation, str.title]  #一系列的函数名

def clean_strings(strings, ops):
    result = []
    for value in strings:
        for function in ops:   #遍历调用列表中的函数
            value = function(value)
        result.append(value)
    return result
```
等同于
```
def clean_strings(strings):
    result = []
    for value in strings:
        value = value.strip()
        value = re.sub('[!#?]', '', value)
        value = value.title()
        result.append(value)
    return result
```
我们还可以将函数作为另一个函数的参数，例如内置的map函数，它的作用是在一组数据上应用一个函数：
```
In [10]: def change(value):
    return str(str(value) + "wt")
   ....:

In [7]: list = [1,2,3]

In [11]: for i in map(change,list):    #将list的元素经过change()处理之后，再赋值给 i
    print(i)
   ....:     
1wt
2wt
3wt
```
同时，我们还可以使用lambda匿名函数，这种用法要比完整的函数声明和定义要简洁得多
```
In [3]: def double(list,f):
    return [f(x) for x in list]
   ...:

In [4]: print(double(my_list,lambda x:x*2))
[2, 4, 6]
```
***
# 柯里化，部分参数的应用
柯里化是一个计算机科学术语，它的意思就是部分参数应用，由现有的函数组合成一个新的函数
```
In [5]: def add(x,y):
   ...:     return x+y
   ...:

In [6]: add_one = lambda x: add(x,1)    #派生出一个新函数

In [7]: print(add_one(2))
3
```
等同于
```
In [9]: from functools import partial
In [12]: add_one = partial(add,y=1)

In [13]: print(add_one(2))
3
```
# 生成器表达式
```
In [189]: gen = (x ** 2 for x in range(100))
```
# itertools模块
![常用的函数](https://upload-images.jianshu.io/upload_images/13918038-8c62158fd834ea40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
***
# 读写文件
读写文件一般使用with语句，不仅方便读取数据，而且在退出代码块之后会自动清理文件
```
In [212]: with open(path , "r") as f:     #读文件
   .....:     lines = [x.rstrip() for x in f]

In [225]: with open('path'.txt', 'w') as handle:    #写文件
   .....:     handle.writelines(x for x in open(path) if len(x) > 1)
```
![Python的文件模式](https://upload-images.jianshu.io/upload_images/13918038-063415f3fc29c4ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Python重要的文件方法](https://upload-images.jianshu.io/upload_images/13918038-e4759e51d168f515.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
