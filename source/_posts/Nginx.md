---
title: Nginx
copyright: true
date: 2019-06-05 14:20:12
tags: 运维
---

# Nginx简介
Nginx是一款高性能Http服务器、反向代理服务器以及电子邮件（IMAP、POP3）代理服务器，能支撑5万并发，CPU、内存等资源消耗非常低，运行稳定。

# Nginx应用场景
- HTTP服务器：Nginx是一个HTTP服务，可以独立提供HTTP服务，可以做网页静态服务器。
- 虚拟主机：可以实在一台服务器虚拟出多个网站。
- 反向代理、负载均衡：可以使用Nginx反向代理到多个集群。

# docker-compose.yml
```yml
version: '3.1'
services:
        tomcat9090:
                image: tomcat
                container_name: tomcat9090
                restart: always
                ports:
                    - 9090:8080
        tomcat9091:
                image: tomcat
                container_name: tomcat9091
                restart: always
                ports:
                    - 9091:8080
        nginx:
                image: nginx
                container_name: nginx
                restart: always
                ports:
                    - 80:80
                    - 9000:9000
                volumes:
                    - ./conf/nginx.conf:/etc/nginx/nginx.conf
                    - ./web:/usr/share/nginx/wwwroot
```

# 虚拟主机
虚拟主机是一种特殊的软硬件技术，它可以将网络上的每一台计算机分成多个虚拟主机，每个虚拟主机可以独立对外提供www服务，这样就实现一台主机对外提供web服务。

通过Nginx可以实现虚拟主机的配置，Nginx支持三种类型的虚拟主机的配置
- 基于IP的虚拟主机
- 基于域名的虚拟主机
- 基于端口的虚拟主机

#### 配置文件
```
# CPU多少核就填多少核，充分利用CPU资源
worker_processes 1;

events {
        worker_connections 1024;
}

http {
        include   mime.types;
        default_type   application:octet-stream;

        sendfile   on;

        keepalive_timeout  65;

        server {
                listen 80;
                # 基于 IP
                server_name  172.28.7.36;

                # 基于域名
                # server_name endalin.com;
                location / {
                        root    /usr/share/nginx/wwwroot/welcome;
                        index index.html;
                }
        }

        server {
                # 基于端口
                # listen 81;
                listen 80;
                server_name  172.28.7.36;

                # 基于域名
                # server_name oj.endalin.com;
                location / {
                        root    /usr/share/nginx/wwwroot/welcome81;
                        index index.html;
                }
        }
}

```

# 反向代理、负载均衡
反向代理服务器架设在服务器端，通过缓存经常被请求的页面来缓解服务器的工作量，将客户机请求转发给内部网络上的目标服务器，并将从服务器上得到的结果返回给Internet上请求连接的客户端，此时代理服务器和目标主机对外表现为一个服务器。

#### docker-compose.yml
```yml
version: '3.1'
services:
        tomcat9090:
                image: tomcat
                container_name: tomcat9090
                restart: always
                ports:
                    - 9090:8080
        tomcat9091:
                image: tomcat
                container_name: tomcat9091
                restart: always
                ports:
                    - 9091:8080
        nginx:
                image: nginx
                container_name: nginx
                restart: always
                ports:
                    - 80:80
                    - 9000:9000
                volumes:
                    - ./conf/nginx.conf:/etc/nginx/nginx.conf
                    - ./web:/usr/share/nginx/wwwroot

```

#### 配置文件
```
worker_processes 1;

events {
	worker_connections 1024;
}

http {
        include   mime.types;
        default_type   application:octet-stream;

        sendfile   on;

        keepalive_timeout  65;

        upstream tomcatServer{
				        server 10.42.29.120:9000 weight=10;
								server 10.42.29.120:9091 weight=10;
				}

        server {
                listen 80;
                server_name 10.42.29.120;
                location / {
                        proxy_pass http://tomcatServer;
                        index index.html index.jsp;
                }
        }
}
```
