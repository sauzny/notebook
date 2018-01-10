# 将本地jar包加入本地仓库中

thrift 0.5.0 加入本地maven仓库中

## 一、下载

在Twitter中下载jar包和pom文件。

http://maven.twttr.com/thrift/libthrift/0.5.0/libthrift-0.5.0.jar

http://maven.twttr.com/thrift/libthrift/0.5.0/libthrift-0.5.0.pom

libthrift-0.5.0.pom中的内容截取：

```
<groupId>thrift</groupId>
<artifactId>libthrift</artifactId>
<version>0.5.0</version>
```

## 二、加入

我把`libthrift-0.5.0.jar`,按照本地仓库的路径放好。

加入命令：

```
mvn install:install-file -Dfile="E:\m2\repository\org\apache\thrift\libthrift\0.5.0\libthrift-0.5.0.jar" -DgroupId=thrift -DartifactId=libthrift -Dversion=0.5.0 -Dpackaging=jar
```