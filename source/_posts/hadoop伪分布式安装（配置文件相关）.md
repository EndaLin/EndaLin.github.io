---
title: hadoop伪分布式安装（配置文件相关）
copyright: true
date: 2019-06-20 15:06:09
tags: hadoop
---


# 推荐使用docker去部署hadoop集群

[Github - Big Data Europe](https://github.com/big-data-europe)

<!--more-->

# 手动安装

> /etc/hadoop

#### hadoop-env
指定JDK 路径

```sh
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-2.el7_6.x86_64
```

#### core-site.xml

```xml
<configuration>
  <!--hdfs主节点地址-->
  <property>
	  <name>fs.defaultFS</name>
	  <value>hdfs://namenode:9000</value>
  </property>

  <!--指定hadoop运行时产生文件的存储目录-->
  <property>
	  <name>hadoop.tmp.dir</name>
	  <value>file:/root/hadoop/data/</value>
  </property>
</configuration>
```

#### hdfs-site.xml
```xml
<configuration>
    <!--hdfs副本数-->
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

#### mapred-site.xml
```xml
<configuration>
  <!--指明mapreduce的资源调度程序-->
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>
```

#### yarn-site.xml
```xml
<configuration>

<!-- Site specific YARN configuration properties -->
<!--yarn主结点-->
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>resourcemanager</value>
    <!--yarn主结点-->
	</property>
  <!--指定map传递数据给reduce的机制-->
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>

</configuration>

```
