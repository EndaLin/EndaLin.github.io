---
title: docker| 数据管理
date: 2019-03-06 20:57:57
tags: docker
---
# 数据管理
在容器中管理数据的方式主要有两种
> 数据卷
挂载主机目录

## 数据卷
- 定义：数据卷是一个可以供一个或者多个容器使用的**特殊目录**
- 特点
> 可以在容器之间共享和重用
> 对数据卷的修改会马上生效
> 对数据卷的更新不会影响镜像
> 数据卷会一直存在，即使容器被删除

#### 创建一个数据卷
- 命令
> sudo docker volume create [volume_name]

#### 查看所有的数据卷
- 命令
> sudo docker volume ls

#### 查看指定数据卷的信息
-命令
> sudo docker volume inspect [volume_name]

#### 启动一个加载数据卷的容器
- 命令
> sudo docker run -d　\
 -P 81:80　\
 --name web　\
 --mount source=[volume_name],target=[target_name]　\
 [Image_name]　

- -d后台运行
- -P端口映射
- --mount将[volume_name]数据卷加载到容器的[target_name]目录下
- [Image_name]镜像名
- app.py主体程序

#### 查看数据卷的具体信息
- 命令
> sudo docker inspect [container_name]

#### 删除镜像
- 命令
> sudo docker volume rm [volume_name]

## 挂载主机目录
#### 挂载一个主机目录作为数据卷
- 命令
> sudo docker run -d　\
 -P 81:80　\
 --name web　\
 -v /src/webapp:/opt/webapp　
 [Image_name]　

- Docker挂载目录默认的权限是读写，ro是只读
