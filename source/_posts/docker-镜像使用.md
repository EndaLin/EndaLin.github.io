---
title: docker| 镜像使用
date: 2019-03-06 20:56:30
tags: docker
---
# 使用镜像
#### 从 Docker 镜像仓库获取镜像的命令
- 命令格式
> sudo docker pull [选项] [url : 端口号]/镜像名[:标签]
- 此处的url 默认为Docker Hub
- 标签可忽略，默认为latest
- 举个例子 **$ docker pull ubuntu:16.04**

#### 运行容器（容器是镜像的一个实例）
- 输入命令
> sudo docker run -it -rm 镜像名[:标签] bash
- -it：这里包括两个参数
> -i：交互式参数
> -t：终端
- --rm：这个参数是说容器退出之后随之将其删除
- bash启动交互式shell
- exit命令退出

#### 列出镜像
- 命令
> sudo docker images
- 列表包含了 仓库名（**镜像名**）、标签、镜像 ID、创建时间 以及 所占用的空间
- 镜像 ID 则是镜像的唯一标识
- 一个镜像可以对应多个标签
- docker images 列表中的镜像体积总和并非是所有镜像实际硬盘消耗
> 因为不同镜像可能会因为使用相同的基础镜像，从而拥有共同的层，相同的层只需要保存一份即可，因此实际镜像硬盘占用空间很可能要比这个列表镜像大小的总和要小的多

#### 镜像体积
- 宿主标识的镜像空间和Docker Hub上的不同，Docker Hub上的是经过压缩的
> 命令：docker system df
> 可查看镜像、容器、数据卷所占用的空间

#### 虚悬镜像
- 这个镜像既没有仓库名，也没有标签，均为 <none>

#### 删除镜像
- 命令
> sudo docker image rm <Image_ID>
- ID一般取前3个字符以上，只要足够区分于别的镜像便可成功删除
- 可以添加 -f 参数,可删除正在运行的容器
- docker container prune 命令可以清理所有处于终止状态的容器
