---
title: Dockerfile 相关指令
date: 2019-03-06 20:55:54
tags: docker
---
# Dockerfile指令全解

### FROM
> 指定基础镜像
注：scratch是空白镜像
如：FROM mysql
### RUN
> 执行命令
> 每一个run指令都会新建一层，并在其上执行这些命令，执行接收，commit这一层的修改，便构成了新的镜像
> 如:RUN apt-get update
> 一般来说，只需构建一层便可
举个例子
> FROM debian:jessie
  RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \

### 构建镜像
> 在**Dockerfile**文件所在的目录执行命令
> **docker build [选项] <上下文路径/URL/->**
> 例如　docker build -t nginx:v3 .
其中
- －t nginx:v3 是指定镜像名称
- 最后一个 . 是指当前目录
