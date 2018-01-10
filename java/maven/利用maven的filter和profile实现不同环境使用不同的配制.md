# 利用maven的filter和profile实现不同环境使用不同的配制

在我们平常的java开发中，会经常使用到很多配制文件（xxx.properties，xxx.xml），而当我们在本地开发（dev），测试环境测试（test），线上生产使用（product）时，需要不停的去修改这些配制文件，次数一多，相当麻烦。现在，利用maven的filter和profile功能，我们可实现在编译阶段简单的指定一个参数就能切换配制，提高效率，还不容易出错，详解如下。

## 一，原理：

- 利用filter实现对资源文件(resouces)过滤

	maven filter可利用指定的xxx.properties中对应的key=value对资源文件中的${key}进行替换，最终把你的资源文件中的username=${key}替换成username=value

- 利用profile来切换环境

	maven profile可使用操作系统信息，jdk信息，文件是否存在，属性值等作为依据，来激活相应的profile，也可在编译阶段，通过mvn命令加参数 -PprofileId 来手工激活使用对应的profile
	结合filter和profile，我们就可以方便的在不同环境下使用不同的配制

## 二，配制：

在工程根目录下添加3个配制文件：

```
config-dev.properties  		-- 开发时用
config-test.properties  		-- 测试时用
config-product.properties 	-- 生产时用
```

工程根目录下的pom文件中添加下面的设置：

```
<build>
	<resources>
		<!-- 先指定 src/main/resources下所有文件及文件夹为资源文件 -->
		<resource>
			<directory>src/main/resources</directory>
			<includes>
				<include>**/*</include>
			</includes>
		</resource>
		<!-- 设置对auto-config.properties，jdbc.properties进行过虑，即这些文件中的${key}会被替换掉为真正的值 -->
		<resource>
			<directory>src/main/resources</directory>
			<includes>
				<include>auto-config.properties</include>
				<include>jdbc.properties</include>
			</includes>
			<filtering>true</filtering>
		</resource>
	</resources>
</build>
<profiles>
	<profile>
		<id>dev</id>

		<!-- 默认激活开发配制，使用config-dev.properties来替换设置过虑的资源文件中的${key} -->
		<activation>
			<activeByDefault>true</activeByDefault>
		</activation>
		<build>
			<filters>
				<filter>config-dev.properties</filter>
			</filters>
		</build>
	</profile>
	<profile>
		<id>test</id>
		<build>
			<filters>
				<filter>config-dev.properties</filter>
			</filters>
		</build>
	</profile>
	<profile>
		<id>product</id>
		<build>
			<filters>
				<filter>config-product.properties</filter>
			</filters>
		</build>
	</profile>
</profiles>
```

## 三，使用：

开发环境：

filter是在maven的compile阶段执行过虑替换的，所以只要触发了编译动作即可，如果像以前一样正常使用发现没有替换，则手工clean一下工程（eclipse -> Project -> Clean）【这里你应该要安装上maven插件，因为替换是maven做的，不是eclipse做的，所以这里的clean应当是触发了maven的compile】，然后在Tomcat上也右键 -> Clean一下即可，然后你去tomcat目录下会发现你的工程的资源文件里面的${key}被替换为对应的config-xx的值了
	
如果上面还不行，那么就使用maven插件或者手工控制台下打maven编译命令吧
	
因为pom.xml中设置了dev为默认激活的，所以默认会把config-dev拿来进行替换${key}

- 测试环境

	手工编译，打包：maven clean install -Ptest		-- 激活id="test"的profile

- 生产环境

	手工编译，打包：maven clean install -Pproduct		-- 激活id="product"的profile