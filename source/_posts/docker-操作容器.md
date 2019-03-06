---
title: docker| 操作容器
date: 2019-03-06 20:57:36
tags: docker
---
# 操作容器
#### 启动容器
- 启动容器有两种方式
> 1. 基于镜像新建一个容器并启动
> 2. 在终止状态（stopped）的容器重新启动

#### 新建启动并在后台运行
- 命令
> sudo docker run －d --name [name] 镜像名[:标签]　　
- -d：后台运行，启动后会进入容器
- --name可为容器取名，等同与容器ID
- 输出结果可以用 docker logs 查看
> 命令:
> docker logs [OPTIONS] [container ID or NAMES]
> -f：跟踪日志输出
- 使用docker run创建容器是，Docker在后台运行的标准操作包括:
> 检查本地是否存在指定的镜像，不存在就从公有仓库下载
> 利用镜像创建并启动一个容器
> 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
> 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
> 从地址池配置一个 ip 地址给容器
> 执行用户指定的应用程序
> 执行完毕后容器被终止

#### 终止运行中的容器
- 命令
> sudo docker container stop <Container_ID>

#### 查看容器信息
- 命令
> sudo docker container ls
- 加-a　可以查看被终止的容器信息

#### 启动被终止的容器
- 命令
> sudo docker container start <Container_ID>

#### 重启容器
- 命令
> sudo docker container restart

#### 进入容器
- 命令
> docker exec -it <Container_ID> bash
- exit命令退出

#### 导出容器
- 命令
> docker export <Container_ID> **>** <local_file_name>
例如：docker export 7691a814370e > ubuntu.tar

#### 导入容器
- 命令
> cat <local_file_name> | docker import - <仓库名>:<TAG>
例如：
cat ubuntu.tar | docker import - test/ubuntu:v1.0
or
docker import http://example.com/exampleimage.tgz example/imagerepo

#### 删除容器
- 命令
> sudo docker rm -v [container_name]
- -v删除容器的同时移除数据卷
