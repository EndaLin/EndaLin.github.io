---
title: docker| 基本概念
date: 2019-03-06 20:56:53
tags: docker
---
# docker

#### what is docker
- Docker是基于Go语言开发实现的，是一种对进程进行封装隔离，属于操作系统层面的虚拟化技术
- 由于隔离的进程独立于宿主和其它隔离的进程，Docker也因此被称为容器

#### The discrimination between virtual machines and docker
- 传统虚拟机技术是虚拟出一套硬件，在其运行一个完整的操作系统，然后在这个系统运行所需的应用进程
- Docker的应用进程是直接运行为宿主的内核上，容器内没有自己的内核，更没有硬件虚拟
- Docker容器比传统的虚拟机更为轻便

<!--more-->

#### The discrimination between virtual machines and docker
![Virtual_Machines](https://upload-images.jianshu.io/upload_images/13918038-0362e4f59b7bd67f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![docker](https://upload-images.jianshu.io/upload_images/13918038-5fb043f42f9f3caa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### The advantage of Docker
- 容器不需要硬件模拟或者运行操作系统等额外开外，故相比与传统的虚拟机技术，一个相同配置的主机，可以运行更多数量的容器
- 容器直接运行于宿主内核，无需启动完整的操作系统，因此可以做到毫秒级的启动
- 容器可以提供一致的开发环境
- 使用容器可以定制镜像来实现持续集成，交付和部署

![总结对比](https://upload-images.jianshu.io/upload_images/13918038-cb088fcf5676ac6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### Docker Image
- Docker镜像(Image)，就相当于是一个root文件系统
- 镜像使用的是分层存储
> 例如：官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu 16.04 最小系统的 root 文件系统　　

#### Docker Container
- 镜像(Image)和容器(Container)的关系，就相当于面向对象程序设计的**类**和**实例**一样
- 镜像是静态的定义
- 容器是镜像运行时的实体
- 容器可以被创建，启动，停止，删除，暂停等等
- 容器是一个运行在隔离环境下的进程，与其它进程不同的是，容器可以拥有只属于自己的root文件系统,自己的网络配置，自己的进程空间以及自己的ID空间等等
- 如同镜像一样，容器也是使用分层存储，每一个容器运行时，以镜像为基础层，在其上创建一个当前容器的存储层，可称为容器运行时而准备的存储层，又称容器存储层
- 容器消亡时，容器存储层也随之消亡，**故，容器不应向存储层写入任何数据需保持无状态化**
- 所以的文件写入操作，都应该使用**数据卷**,或者**绑定宿主目录**,在这些位置的读写会跳过容器存储层，直接对宿主或者网络存储发生读写
- 容器消亡，数据卷不会消亡，使用数据卷后，容器删除或者重新运行之后，数据不会丢失

#### Docker Register
- 一个 Docker Registry 中可以包含多个仓库（Repository）
- 每个仓库可以包含多个标签（Tag）,每个标签对应一个镜像，如版本标签,如果忽略标签，则使用latest作为默认标签
- Docker Hub
- 阿里云加速器
