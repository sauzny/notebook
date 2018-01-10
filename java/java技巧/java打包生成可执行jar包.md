# java打包生成可执行jar包

## 一、打包结果

打包之后，生成两个目录和一个jar文件

- config，配置文件目录，xx.properties，xx.xml
- lib，依赖的 jar 包目录
- xxxx.jar

生成config目录原因，配置文件单独在外部，方便修改。

生成lib目录原因，更新项目时，依赖不更新的情况下只更新 xxx.jar 即可，更新比较快。

## 二、MANIFEST.MF

jar包中的文件MANIFEST.MF需要明确写出

- Main-Class，main函数所在的类
- Class-Path，运行时依赖的包

Class-Path，需要写明

- config目录所在位置。以 `/` 结尾
- 每一个jar包所在的位置，以 `.jar` 结尾

各项之间使用 ` ` 空格分开

## 三、其他

### 3.1 关于classpath指定依赖

对于.class和其他配置文件来说，只需要指明包的路径即可

对于jar文件来说，必须要指定全路径即路径+文件名的格式，不能只指定一个路径。

### 3.2 关于config目录

==注意：== 不要误解config目录是classpath！！！

MANIFEST.MF中Class-Path属性，不写config目录所在位置，也能启动程序（读取到application.properties），原因如下：

SpringApplication  将从以下位置加载 application.properties  文件，并把
它们添加到Spring  Environment  中：

1. 当前目录下的 /config  子目录。
2. 当前目录。
3. classpath下的 /config  包。
4. classpath根路径（root）。

该列表是按优先级排序的（列表中位置高的路径下定义的属性将覆盖位置低的）。
