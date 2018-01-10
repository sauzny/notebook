# maven编译跳过测试

## 1、命令行

```shell
mvn install -Dmaven.test.skip=true
```

## 2、配置文件

在项目中的pom文件中配置<skip>true</skip>

```xml
<project>
  [...]
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <configuration>
          <skip>true</skip>
        </configuration>
      </plugin>
    </plugins>
  </build>
  [...]
</project>
```
