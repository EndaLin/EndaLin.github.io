---
title: MySQL三范式
copyright: true
date: 2019-06-08 10:13:16
tags: MySQL
---

# 第一范式（1st NF - 列都是不可再分的）
要求：第一范式的目标是确保每一列的原子性，每一列都是不可再分的最小数据单元。
![image.png](https://upload-images.jianshu.io/upload_images/13918038-a4805de719007f34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 第二范式（2st NF - 每张表只描述一件事情）
前提： 满足第一范式  
要求： 表中的非主键列不存在对主键的部分依赖。
![image.png](https://upload-images.jianshu.io/upload_images/13918038-87bd740f52b438b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 第三范式（3st NF - 不存在对非主键列的传递依赖）
前提：满足第二范式
要求：表中的列不存在对非主键列的传递依赖。
![image.png](https://upload-images.jianshu.io/upload_images/13918038-dc38fcd4c2b1c71a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 参考
[数据库，部分函数依赖，传递函数依赖，完全函数依赖，三种范式的区别](https://blog.csdn.net/rl529014/article/details/48391465)
