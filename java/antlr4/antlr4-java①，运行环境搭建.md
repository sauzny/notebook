# antlr4-java①，运行环境搭建

## 一、工作流程

- 创建一个antlr4 project，编写g4文件
- 运行antlr4 project，生成java文件
- 拷贝生成好的java文件到所需要的项目中使用

## 二、antlr4+eclipse环境搭建

### 安装插件

Eclipse Marketplace中安装插件

- ANTLR 4 IDE 版本：0.3.6
- Eclipse Xtext 版本：2.11.0

### 使用说明

安装之后，在 `new -> project -> General` 中出现 `ANTLR 4 project`。

新建项目，在项目中出现 `Hello.g4` 文件，在此文件上右键 `Run`，生成 `target` 文件夹。

(右击点击 `run as -> External tools configurations`，填写参数 `-listener -visitor -encoding UTF-8`)

生成 `target` 文件夹中包含一些java文件和tokens文件。

至此，antlr4+eclipse环境搭建搭建完成，可以运行antlr4项目，生成java文件。

==注意：== 大部分人更推荐使用，intellij idea plugin

## 三、antlr4+intellij idea环境搭建

### 下载插件

地址：https://plugins.jetbrains.com/plugin/7358-antlr-v4-grammar-plugin

下载一个zip包：intellij-plugin-v4.zip

### 安装插件

`intellij idea` -> `settings` -> `Plugins` -> `Install plugin from disk...`

选择 intellij-plugin-v4.zip，然后 OK 即可

### 使用说明

新建java项目，在项目中新建一个g4w文件。

#### 测试规则

在文件内容中右键选择 `Test Rule XXX`

如图：

左下角输入内容，右下角展示语法树结构。

![image](http://picabstract.preview.ftn.qq.com:8080/ftn_pic_abs_v2/a3829d85ea1a598c09fec7d6bb57b1a5836d58bdc7df3fe9a86cf72d60033f2e7cf475b193ca781ee554424089fafc2b?pictype=scale&from=30012&version=2.0.0.2&uin=361376366&fname=antlr4-idea-01.PNG&size=1024)


#### 生成java文件

选择左侧g4文件，右键选择 `Generate ANTLR Recognizer` ，则生成文件。