---
title: 利用Python进行数据分析－NumPy基础
copyright: true
date: 2019-05-30 12:59:21
tags: Python
---
>本文出自＜利用Python进行数据分析　第２版＞　　侵删

# NumPy
NumPy　是Python数值计算最重要的基础包，可以高效处理大数组的数据．

# NumPy的ndarray：一种多维的数组对象
ndarray是一个快速而又灵活的同构数据多维容器，是一个Ｎ维数组对象，其中所有的元素对象必须要是相同的数据类型，每一个对象包含一个元组和一个属性，分别是shape（一个表示各维度大小的元组）和dtype（一个说明数组数据类型的对象）

<!--more-->

# 创建ndarray
创建ndarray最简单的方法是使用array函数，它接受一切序列型的对象（包括其它数组），然后产生一个新的含有传入数据的NumPy数组
```
In [1]: list1 = (1,2,3)
In [3]: import numpy as np
In [5]: data = np.array(list1)

In [6]: data
Out[6]: array([1, 2, 3])
```
嵌套序列会被转换成一个多维的数组，array函数会为新建的数组推断出一个较为合适的数据类型，数据类型保存在一个特殊的dtype对象中
```
In [7]: list2 = [[1,2,3],[4,5,6]]

In [8]: data2 = np.array(list2)

In [9]: data2
Out[9]:
array([[1, 2, 3],
       [4, 5, 6]])

In [10]: data2.shape  #查看维度大小
Out[10]: (2, 3)

In [11]: data2.dtype　#查看元素类型
Out[11]: dtype('int64')

```
# 常用函数
#### zeros：创建指定长度或者形状全为０的数组
#### ones：创建指定长度或者形状全为１的数组
#### empty：可以创建一个没有任何具体值的数组
使用这些方法创建数组，只需要传入一个可以表示形状的元组便可
```
In [12]: np.zeros((10))
Out[12]: array([ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.])

In [13]: np.ones((2,3))
Out[13]:
array([[ 1.,  1.,  1.],
       [ 1.,  1.,  1.]])

In [14]: np.empty((2,3,4))
Out[14]:
array([[[  6.94667955e-310,   4.65986064e-310,   6.94667972e-310,
           6.94667971e-310],
        [  6.94667972e-310,   6.94667971e-310,   6.94667852e-310,
           6.94667972e-310],
        [  6.94667852e-310,   6.94667852e-310,   3.55727265e-321,
           5.53353523e-322]],

       [[  0.00000000e+000,   6.94667867e-310,   6.94666603e-310,
           6.94666605e-310],
        [  6.94666603e-310,   6.94667969e-310,   6.94666603e-310,
           6.94667725e-310],
        [  6.94667974e-310,   6.94666603e-310,   6.94667974e-310,
           6.94667971e-310]]])
```
#### arange是Python内置函数range的数组版
```
In [15]: np.arange(10)
Out[15]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
```
![数组创建函数](https://upload-images.jianshu.io/upload_images/13918038-989f18bfae94317c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
备注：数据类型基本都是float64(浮点数)
![NumPy的数据类型](https://upload-images.jianshu.io/upload_images/13918038-b972d8d088894734.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![NumPy的数据类型](https://upload-images.jianshu.io/upload_images/13918038-0fb878cc77a58b9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 创建时可以指定类型
```
In [21]: data2 = np.array([1,2,3],dtype=np.float64)

In [22]: data2
Out[22]: array([ 1.,  2.,  3.])

In [23]: data2.dtype
Out[23]: dtype('float64')
```
#### 创建后可以修改类型，astype函数
```
In [24]: data2.astype(int)
Out[24]: array([1, 2, 3])

In [25]: data2.dtype   #注意，astype只会返回转换后的类型，但并不会实际地去转换元素类型  
Out[25]: dtype('float64')
```
#NumPy 数组的运算
数组不需要通过循环便可执行批量运算
```
In [51]: arr = np.array([[1., 2., 3.], [4., 5., 6.]])

In [52]: arr
Out[52]:
array([[ 1.,  2.,  3.],
       [ 4.,  5.,  6.]])

In [53]: arr * arr 　　#乘
Out[53]:
array([[  1.,   4.,   9.],
       [ 16.,  25.,  36.]])

In [54]: arr - arr　　#减
Out[54]:
array([[ 0.,  0.,  0.],
       [ 0.,  0.,  0.]])
In [55]: 1 / arr　　#除
Out[55]:
array([[ 1.    ,  0.5   ,  0.3333],
       [ 0.25  ,  0.2   ,  0.1667]])

In [56]: arr ** 0.5　#指数
Out[56]:
array([[ 1.    ,  1.4142,  1.7321],
       [ 2.    ,  2.2361,  2.4495]])
```
大小相同的数组可以产生布尔值数组
大小不同的数组之间的运算叫做广播
```
In [57]: arr2 = np.array([[0., 4., 1.], [7., 2., 12.]])

In [58]: arr2
Out[58]:
array([[  0.,   4.,   1.],
       [  7.,   2.,  12.]])

In [59]: arr2 > arr
Out[59]:
array([[False,  True, False],
       [ True, False,  True]], dtype=bool)
```
# 切片和索引
NumPy产生的切片是视图，而并非是新的对象，当将一个标量值传给一个切片时，如arr[5:8]=12时，该值会自动传播到整个选区，这意味这在视图上，任意数据的修改都会影响到源数组
```
In [32]: data2 = np.array([1,2,3])

In [33]: data3 = data2[1:2]

In [34]: data3
Out[34]: array([2])

In [35]: data3[0] = 12     #通过索引去赋值，也可以通过索引去访问

In [36]: data3
Out[36]: array([12])

In [37]: data2
Out[37]: array([ 1, 12,  3])
```
切片［：］会给所有值赋值，例如arr[:] = 13
NumPy对数据的处理不包括复制粘贴的优势将体现在处理大规模的数据中 ，如果我们需要的是一份副本而不是一个视图的话，我们可以使用arr[5:8].copy()
索引的返回值可为元素也可为数组
```
In [39]: arr
Out[39]:
array([[[ 1,  2,  3],
        [ 4,  5,  6]],

       [[ 7,  8,  9],
        [10, 11, 12]]])

In [40]: arr[1]
Out[40]:
array([[ 7,  8,  9],
       [10, 11, 12]])

In [41]: arr[1,1]    #访问索引以（１，１）开头的那些值
Out[41]: array([10, 11, 12])

In [45]: arr[:1,1:,2:3]　　#切片的用法基本一致
Out[45]: array([[[6]]])

```
![切片示意图](https://upload-images.jianshu.io/upload_images/13918038-c21dca76a3df33dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 布尔型索引
常用于数据的匹配
```
In [46]: names = np.array(['wt','Bob'])

In [47]: scores = np.array([90,80])

In [48]: names == 'wt'
Out[48]: array([ True, False], dtype=bool)

In [49]: scores[names == 'wt']
Out[49]: array([90])
```
注意：布尔型数组的长度需要匹配对应数组的最高维度，在匹配的同事，我们还可以索引列，例如scores[name == 'wt',2:]，还可以通过这样来赋值，例如：scores[names == 'wt'] = 0
备注：～操作符可以用来反转条件，例如cond = names == 'wt'，~cond就等同于names != 'wt'
还有一点值得注意的是：Python关键字and和or在布尔型数组中无效，要使用＆和｜．

# 花式索引
# 数组装置
转置是重塑的一种特殊形式，它返回的是源数据的视图，不会进行任何的复制操作，转置使用数组的Ｔ属性
高维数组的装置需要使用transpose
```
array([[ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.]])

In [11]: arr.T
Out[11]:
array([[ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.]])
```
# 计算矩阵内积
使用np.dot(arr1, arr2)来计算两个矩阵的内积
# 通用函数：快速的元素级数组函数
通用函数是一种对ndarray中的数据执行元素级运算的函数
```
In [15]: arr1 = np.array([2,2])

In [16]: np.sqrt(arr1)
Out[16]: array([ 1.41421356,  1.41421356])
```

![常用的ufunc](https://upload-images.jianshu.io/upload_images/13918038-c16ca18c373640a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![常用的ufunc](https://upload-images.jianshu.io/upload_images/13918038-a3e05d1ba5a77eee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![常用的ufunc](https://upload-images.jianshu.io/upload_images/13918038-55566c55ac395f12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![常用的ufunc](https://upload-images.jianshu.io/upload_images/13918038-32ccede03dd82010.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![常用的ufunc](https://upload-images.jianshu.io/upload_images/13918038-ef4600a822a2f32f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 将条件逻辑表述为数组运算
假设我们想根据cond中的值来选取xarr和yarr的值
```
In [29]: xarr = np.array([1.1,1.2,1.3,1.4,1.5])

In [30]: yarr = np.array([2.1,2.2,2.3,2.4,2.5])

In [31]: cond = np.array([True,False,True,True,False])

In [32]: result = [(x if c else y) for x,y,c in zip(xarr,yarr,cond)]

In [33]: result
Out[33]: [1.1000000000000001, 2.2000000000000002, 1.3, 1.3999999999999999, 2.5]
```
等同于
```
In [34]: result = np.where(cond,xarr,yarr)

In [35]: result
Out[35]: array([ 1.1,  2.2,  1.3,  1.4,  2.5])
```
np.where的第二个和第三个不必是数组，它们都可以是标量值．在数据分析的工作中，where的工作通常是根据一个数组产生另一个数组．假设一个由随机数组组成的矩阵，大于零的数都变成３，小于０的数都变成－３，若此时用利用where，则会变得非常方便
```
In [36]: arr = np.random.randn(6,6)

In [37]: arr
Out[37]:
array([[-1.45536948, -0.01610929, -0.02849423, -0.82497092,  1.05006367,
        -0.20924655],
       [-0.4434815 , -0.33147041, -0.61486327, -0.5423556 ,  1.512384  ,
        -1.35921009],
       [-0.53875138, -0.25256538,  0.32190533, -0.20779243,  0.48525456,
         0.97019284],
       [ 0.12193935, -0.26348046,  0.86740783, -0.32927907,  0.35186663,
         2.24697225],
       [-0.49439342,  0.38880278,  0.52902035,  0.86600846,  1.31413569,
         0.58566283],
       [ 0.34011322,  0.96141724, -1.00353822, -0.30896308, -1.03500063,
        -0.43719574]])

In [38]: arr>0
Out[38]:
array([[False, False, False, False,  True, False],
       [False, False, False, False,  True, False],
       [False, False,  True, False,  True,  True],
       [ True, False,  True, False,  True,  True],
       [False,  True,  True,  True,  True,  True],
       [ True,  True, False, False, False, False]], dtype=bool)

In [39]: np.where(arr>0,3,-3)
Out[39]:
array([[-3, -3, -3, -3,  3, -3],
       [-3, -3, -3, -3,  3, -3],
       [-3, -3,  3, -3,  3,  3],
       [ 3, -3,  3, -3,  3,  3],
       [-3,  3,  3,  3,  3,  3],
       [ 3,  3, -3, -3, -3, -3]])

In [40]: np.where(arr>0,3,arr)   #把大于０的数赋值为３，其余赋值arr
Out[40]:
array([[-1.45536948, -0.01610929, -0.02849423, -0.82497092,  3.        ,
        -0.20924655],
       [-0.4434815 , -0.33147041, -0.61486327, -0.5423556 ,  3.        ,
        -1.35921009],
       [-0.53875138, -0.25256538,  3.        , -0.20779243,  3.        ,
         3.        ],
       [ 3.        , -0.26348046,  3.        , -0.32927907,  3.        ,
         3.        ],
       [-0.49439342,  3.        ,  3.        ,  3.        ,  3.        ,
         3.        ],
       [ 3.        ,  3.        , -1.00353822, -0.30896308, -1.03500063,
        -0.43719574]])

In [41]: np.where(arr>0,arr,3)    #把大于０的赋值为arr,其余赋值为３
Out[41]:
array([[ 3.        ,  3.        ,  3.        ,  3.        ,  1.05006367,
         3.        ],
       [ 3.        ,  3.        ,  3.        ,  3.        ,  1.512384  ,
         3.        ],
       [ 3.        ,  3.        ,  0.32190533,  3.        ,  0.48525456,
         0.97019284],
       [ 0.12193935,  3.        ,  0.86740783,  3.        ,  0.35186663,
         2.24697225],
       [ 3.        ,  0.38880278,  0.52902035,  0.86600846,  1.31413569,
         0.58566283],
       [ 0.34011322,  0.96141724,  3.        ,  3.        ,  3.        ,
         3.        ]])

```
# 数学和统计方法

![image.png](https://upload-images.jianshu.io/upload_images/13918038-168d6241b2b875ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/13918038-6030bfac0c34df21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 用于布尔型数组的方法
```
In [47]: arr = np.array([-1,2,-3])

In [48]: (arr > 0).sum()  #计算元素大于０的个数
Out[48]: 1
```
另外还有两个方法any和all，它们对布尔型数组非常有用。any用于测试数组中是否存在一个或多个True，而all则检查数组中所有值是否都是True：
```
In [192]: bools = np.array([False, False, True, False])

In [193]: bools.any()
Out[193]: True

In [194]: bools.all()
Out[194]: False
```
这两个方法也能用于非布尔型数组，所有非0元素将会被当做True。

# 排序
```
In [195]: arr = np.random.randn(6)

In [196]: arr
Out[196]: array([ 0.6095, -0.4938,  1.24  , -0.1357,  1.43  , -0.8469])

In [197]: arr.sort()

In [198]: arr
Out[198]: array([-0.8469, -0.4938, -0.1357,  0.6095,  1.24  ,  1.43  ])
```
# 唯一化以及其它集合逻辑
NumPy提供了一些针对ndarray的基本集合运算，最常用的是np.unique
```
In [56]: arr = np.array(['wt','jm','wt'])   

In [57]: np.unique(arr)　　　#找到唯一值，并返回
Out[57]:
array(['jm', 'wt'],
      dtype='<U2')
```
![常用的集合运算](https://upload-images.jianshu.io/upload_images/13918038-b6137d41de9e6f2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 线性代数
NumPy提供了一个用于矩阵乘法的dot函数（既是一个数组方法也是numpy命名空间中的一个函数）
![与线性代数相关的方法](https://upload-images.jianshu.io/upload_images/13918038-472fc829c5796d26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 伪随机数的生成
numpy.random模块对Python内置的random进行了补充，增添一些可以高效生成多种概率发布的样本值的方法
![常用的函数](https://upload-images.jianshu.io/upload_images/13918038-10ea37a05a3fd1ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![常用的函数](https://upload-images.jianshu.io/upload_images/13918038-e2225612c5e3936a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
