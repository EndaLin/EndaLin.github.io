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
# 正向代理

在客户端（浏览器）配置代理服务器，通过代理服务器访问指定网址

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

# 动静分离

通过location 指定不同的后缀名实现不同的请求转发，通过expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之间的请求和流量。

具体Expires 定义：给一个资源设定一个过期时间，无需服务器去验证，直接通过浏览器自身确定是否过期，不会产生额外的流量。这种方式适合不经常变动的资源。假如我设置了3d,即三天，表示在三天之内访问这个URL，发送一个请求给服务器，对比服务器该文件最后的更新时间，如果更新时间没有发生变化，则不会从服务器中抓取，返回状态码304，如果有修改，则直接从服务器中直接下载，返回状态200

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
				        server 10.60.2.128:9000 weight=10;
								server 10.60.2.128:9091 weight=10;
				}

        server {
                listen 80;
                server_name 10.60.2.128;

								location /welcome81 {
							        	root    /usr/share/nginx/wwwroot/;
                        index index.html;
								}

								location /image {
								       root /usr/share/nginx/wwwroot/;
                       # 列出文件目录
											 autoindex on;
								}
        }
}
```
