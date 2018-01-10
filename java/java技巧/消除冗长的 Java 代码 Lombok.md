# 消除冗长的 Java 代码 Lombok

> 前言：
逛开源社区的时候无意发现的，用了一段时间，觉得还可以，特此推荐一下。
lombok 提供了简单的注解的形式来帮助我们简化消除一些必须有但显得很臃肿的 java 代码。特别是相对于 POJO，光说不做不是我的风格，先来看看吧。

lombok 的官方网址：[http://projectlombok.org/](http://projectlombok.org/)  

官网上有 lombok 三分四十九秒的视频讲解，里面讲的也很清楚了，而且还有文档可以参考。

## lombok 安装

### 1.获取jar包

pom文件引入，获取

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.16</version>
</dependency>
```

或者从官网下载

### 2.eclipse / myeclipse 手动安装 lombok

1. 将 lombok.jar 复制到 myeclipse.ini / eclipse.ini 所在的文件夹目录下
2. 打开 eclipse.ini / myeclipse.ini，在最后面插入以下两行并保存：
```
-Xbootclasspath/a:lombok.jar
-javaagent:lombok.jar
```
3.重启 eclipse / myeclipse

## lombok 注解使用

lombok 提供的注解不多，可以参考官方视频的讲解和官方文档。

Lombok 注解在线帮助文档：[http://projectlombok.org/features/index](http://projectlombok.org/features/index)

下面介绍几个我常用的 lombok 注解：

注解 | 位置 | 描述 | 其他
---|---|---|---
@Data | 类注解 | 提供类所有属性的 getting 和 setting 方法，还提供了equals、canEqual、hashCode、toString 方法 | 常用
@NoArgsConstructor | 类注解 | 为类提供一个无参的构造方法 | 常用
@AllArgsConstructor | 类注解 | 为类提供一个全参的构造方法 | 常用
@RequiredArgsConstructor | 类注解 | 为类提供一个包含必要参数的构造方法 | 常用。属性前使用`@NonNull`，则认为该属性是必要属性
@Slf4j | 类注解 | 为类提供一个 属性名为`log`的日志对象 | 常用
@Log | 类注解 | 为类提供一个 属性名为`log`的日志对象 | 不推荐，推荐使用@Slf4j
@Log4j | 类注解 | 为类提供一个 属性名为`log`的日志对象 | 不推荐，推荐使用@Slf4j
@Log4j2 | 类注解 | 为类提供一个 属性名为`log`的日志对象 | 不推荐，推荐使用@Slf4j
@Setter | 属性注解 | 为属性提供 setting 方法 | -
@Getter | 属性注解 | 为属性提供 getting 方法 | -
@NonNull | 属性注解、参数注解 | 判断null，变量为null，则会throw空指针异常 | 不推荐，业务代码中可能需要自己处理异常
@Cleanup | 变量注解 | 不需要写`try` `finally`，自动调用close方法 | -

==注意：==

`@RequiredArgsConstructor` 使用注解参数 `staticName`

`@RequiredArgsConstructor(staticName="of")`

则会生产一个private的包含必要参数的构造函数，同时生成一个 `public static Student of(必要参数){}` 的静态方法。

### 下面是简单示例:

#### 1.不使用 lombok 的方案
 
 ```java
 public class Person {

    private String id;
    private String name;
    private String identity;
    
    public String getId() {
		return id;
    }
    public void setId(String id) {
		this.id = id;
    }
    public String getName() {
		return name;
    }
    public void setName(String name) {
		this.name = name;
    }
    public String getIdentity() {
		return identity;
    }
    public void setIdentity(String identity) {
		this.identity = identity;
    }

}

 ```

#### 2.使用 lombok 的方案

```java
@Data
@Slf4j
@NoArgsConstructor
@AllArgsConstructor
public class Person {

    private String id;
    private String name;
    private String identity;
    
}
```

