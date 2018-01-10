# java classpath 通配符

JDK6支持java -cp后面跟通配符'*'，试了一下发现还是需要注意：
 
### 错误方式（Wrong way）：

``` shell
java  -cp /data/apps/lib/*.jar com.chinacache.Main
或者
java  -cp /data/apps/ilb/ com.chinacache.Main
``` 

### 正确方式（right way）：

``` shell
java -cp /data/apps/ilb/* com.chinacache.Main
 ```
 
### 低版本JDK（小于JDK6）

``` shell
java -cp $(echo /data/apps/ilb/*.jar | tr ' ' ':') com.chinacache.Main
 ```
 
PS. 在jar中的MANIFEST.MF文件还是要依次列出jar文件路径的

实际应用：

```

原：exec java -Xmx1024M -Xms512M   -cp conf:$(echo $(ls lib/*) | sed 's/ /:/g') com.tusimple.adnet.provider.AdnetLogicDeliverRunServer

修改为：exec java -Xmx1024M -Xms512M -cp conf:lib/* com.tusimple.adnet.provider.AdnetLogicDeliverRunServer

```