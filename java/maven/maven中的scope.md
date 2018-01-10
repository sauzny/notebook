# maven中的scope

## 表格说明


值 | 描述 | 解释
---|---|---
compile | 编译范围 | ==默认==， 会被打包。
provided  | 已提供范围 | 不传递，也不会被打包。
runtime | 运行时范围 | 编译时不打包，运行时打包。
test | 测试范围 | 测试编译和测试运行阶段可用。
system | 系统范围 | 必须同时提供一个 systemPath 元素，指定一个对于本地系统中JAR文件的路径，Maven 不会在仓库中去寻找它。
import | 引入范围 | 解决多继承的问题，只能用在dependencyManagement里面

### import举例说明

```
<project>  
    <modelVersion>4.0.0</modelVersion>  
    <groupId>com.test.sample</groupId>  
    <artifactId>base-parent1</artifactId>  
    <packaging>pom</packaging>  
    <version>1.0.0-SNAPSHOT</version>  
    <dependencyManagement>  
        <dependencies>  
            <dependency>  
                <groupId>junit</groupId>  
                <artifactid>junit</artifactId>  
                <version>4.8.2</version>  
            </dependency>  
            <dependency>  
                <groupId>log4j</groupId>  
                <artifactid>log4j</artifactId>  
                <version>1.2.16</version>  
            </dependency>  
        </dependencies>  
    </dependencyManagement>  
</project>  
```

然后就可以通过非继承的方式来引入这段依赖管理配置，这种做法与面向对象设计中使用组合而非继承也有点相似的味道。

```
<project>  
    <modelVersion>4.0.0</modelVersion>  
    <groupId>com.test.sample</groupId>  
    <artifactId>base-parent1</artifactId>  
    <packaging>pom</packaging>  
    <version>1.0.0-SNAPSHOT</version>  
    <dependencyManagement>  
        <dependencies>  
            <dependency>  
                <groupId>junit</groupId>  
                <artifactid>junit</artifactId>  
                <version>4.8.2</version>  
            </dependency>  
            <dependency>  
                <groupId>log4j</groupId>  
                <artifactid>log4j</artifactId>  
                <version>1.2.16</version>  
            </dependency>  
        </dependencies>  
    </dependencyManagement>  
</project>  
```