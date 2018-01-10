# gradle标准项目属性

Project 对象提供了一些标准的属性，您可以在构建脚本中很方便的使用他们. 下面列出了常用的属性:

Name | Type | Default Value
--- | --- | ---
project | Project | Project 实例对象
name | String | 项目目录的名称
path | String | 项目的绝对路径
description | String | 项目描述
projectDir | File | 包含构建脚本的目录
build | File | projectDir/build
group | Object | 未具体说明
version | Object | 未具体说明
ant | AntBuilder | Ant实例对象
