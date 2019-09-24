---
title: Spark-sample
copyright: true
date: 2019-09-23 10:39:46
tags: Spark
---

>> Maven
```
<dependency>
  <groupId>org.apache.spark</groupId>
  <artifactId>spark-sql_2.11</artifactId>
  <version>2.3.1</version>
</dependency>
```


# Start Point
Spark 2.x ： 创建一个SparkSession

```java
SparkSession spark = SparkSession
        .builder()
        .appName("Java Spark SQL basic example")
        //.config("spark.some.config.option", "some-value")
        .master("local")
        .getOrCreate();
```

# Create DataFrames from an existing RDD
record.json
```text
{"key":1,"value":"enda"}
```
Application
```Java
Dataset<Row> dataset = spark.read().json("./record.json");

dataset.show();

// show
// +---+-----+
// |key|value|
// +---+-----+
// |  1| enda|
// +---+-----+
```

# 快速生成可执行Jar 包
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>2.3.1</version>
            <configuration>
                <archive>
                    <manifest>
                        <!--运行jar包时运行的主类，要求类全名-->
                        <mainClass>com.dgut.App</mainClass>
                        <!-- 是否指定项目classpath下的依赖 -->
                        <addClasspath>true</addClasspath>
                        <!-- 指定依赖的时候声明前缀 -->
                        <classpathPrefix>./lib/</classpathPrefix>
                        <!--依赖是否使用带有时间戳的唯一版本号,如:xxx-1.3.0-20121225.012733.jar-->
                        <useUniqueVersions>false</useUniqueVersions>
                    </manifest>
                </archive>
            </configuration>
        </plugin>
        <!--把当前项目所有的依赖打包到target目录下的lib文件夹下-->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-dependency-plugin</artifactId>
            <executions>
                <execution>
                    <id>copy</id>
                    <phase>package</phase>
                    <goals>
                        <goal>copy-dependencies</goal>
                    </goals>
                    <configuration>
                        <outputDirectory>${project.build.directory}/lib</outputDirectory>
                        <!--已存在的Release版本不重复copy-->
                        <overWriteReleases>false</overWriteReleases>
                        <!--已存在的SnapShot版本不重复copy-->
                        <overWriteSnapshots>false</overWriteSnapshots>
                        <!--不存在或者有更新版本的依赖才copy-->
                        <overWriteIfNewer>true</overWriteIfNewer>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

# 官方文档
[Spark SQL](https://spark.apache.org/docs/latest/sql-programming-guide.html)
