# maven-compiler-plugin的使用

maven在编译的时候会使用maven-compiler-plugin默认的jdk版本编译。

下面看一下这个插件该怎么使用，指定jdk版本

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <configuration>
        <source>1.6</source> <!-- 源代码使用的开发版本 -->
        <target>1.6</target> <!-- 需要生成的目标class文件的编译版本 -->
        <!-- 一般而言，target与source是保持一致的，但是，有时候为了让程序能在其他版本的jdk中运行(对于低版本目标jdk，源代码中需要没有使用低版本jdk中不支持的语法)，会存在target不同于source的情况 -->
    
        <!-- 这下面的是可选项 -->
        <encoding>UTF-8</encoding> 
        <meminitial>128m</meminitial>
        <maxmem>512m</maxmem>
        <fork>true</fork> <!-- fork is enable,用于明确表示编译版本配置的可用 --> 
        <compilerVersion>1.3</compilerVersion>
        
        <!-- 这个选项用来传递编译器自身不包含但是却支持的参数选项 -->
        <compilerArgument>-verbose -bootclasspath ${java.home}\lib\rt.jar</compilerArgument>
        
    </configuration>
</plugin>
```

很多时候，大家都喜欢在eclipse中直接生成webapp的maven项目，但是这样子会产生很多问题，特别是使用的jdk不是maven默认支持的jdk的时候。

一般情况下，我是先建立一个最基本的maven项目，类型如下

```xml
<packaging>jar</packaging>
```

然后设置好jdk版本，再改为

```xml
<packaging>war</packaging>
```

再用eclipse update一下maven项目，eclipse的maven插件自会帮我们建立好此webapp在当前jdk下支持的各种默认参数，然后再优调一下就可以使用了，会省去很多麻烦事。

