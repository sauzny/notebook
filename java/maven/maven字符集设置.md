# maven字符集设置

默认使用GBK

需要在两个地方做设置

- 1. 在pom.xml文件中增加

```
<properties>
    <!-- 文件拷贝时的编码 -->  
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>  
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>  
    <!-- 编译时的编码 -->  
    <maven.compiler.encoding>UTF-8</maven.compiler.encoding>  
    </properties>
``` 

- 2. 在编译插件中指定

```
<plugin>  
    <artifactId>maven-compiler-plugin</artifactId>  
    <configuration>  
        <source>1.7</source>  
        <target>1.7</target>  
        <!-- 指明编译源代码时使用的字符编码，  
        maven编译的时候默认使用的GBK编码，   
          通过encoding属性设置字符编码，  
          告诉maven这个项目使用UTF-8来编译 -->  
        <encoding>utf8</encoding>   
    </configuration>  
</plugin> 

<plugin>  
    <groupId>org.apache.maven.plugins</groupId>  
    <artifactId>maven-resources-plugin</artifactId>  
    <version>3.0.2</version>  
    <configuration>  
    	<!-- 指定编码格式，否则在DOS下运行mvn命令时当发生文件资源copy时将使用系统默认使用GBK编码 -->  
        <encoding>UTF-8</encoding>
    </configuration>  
</plugin>  

```