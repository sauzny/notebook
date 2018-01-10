# 本地配置使用maven私服

## 配置内容说明

### 一、配置私服地址和下载权限，本地maven配置文件，settings.xml

- servers-server-id
- mirrors-mirror-id

需要保持一致，可拥有下载jar包权限

### 二、激活私服配置，本地maven配置文件，settings.xml

- profiles-profile-id
- activeProfiles-activeProfile

需要保持一致

### 三、配置deploy，上传权限，本地项目的pom文件

- distributionManagement-snapshotRepository-id
- distributionManagement-repository-id

这两个配置，与settings.xml中的`servers-server-id`保持一致


## 文件举例说明

- settings.xml，本地maven中conf目录下的配置文件
- pom.xml，java项目中的配置文件

settings.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>


<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">


	<localRepository>E:/m2</localRepository>


	<servers>
		<!-- 配置私服用户名和密码 -->
		<server>
			<id>adnet-ms</id>
			<username>adnet</username>
			<password>123456</password>
		</server>
	</servers>

	<mirrors>
		<!-- 私服地址 -->
		<mirror>
			<id>adnet-ms</id>
			<mirrorOf>*</mirrorOf>
			<name>adnet-ms</name>
			<url>http://172.16.1.25/nexus/content/groups/public/</url>
		</mirror>
	</mirrors>

	<profiles>

		<profile>
			<id>adnet-sf</id>
			<!-- 配置jar仓库 -->
			<repositories>
				<repository>
					<!-- id名字不能修改，使用此名字保证所有的包只从私服下载 -->
					<id>central</id>
					<url>http://repo.maven.apache.org/maven2</url>
					<releases>
						<enabled>true</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
					</snapshots>
				</repository>
			</repositories>

			<!-- 配置plugin jar仓库 -->
			<pluginRepositories>
				<pluginRepository>
					<!-- id名字不能修改，使用此名字保证所有的包只从私服下载 -->
					<id>central</id>
					<url>http://repo.maven.apache.org/maven2</url>
					<releases>
						<enabled>true</enabled>
					</releases>
					<snapshots>
						<enabled>true</enabled>
					</snapshots>
				</pluginRepository>
			</pluginRepositories>
		</profile>

		<!-- sonar插件，编译项目，代码静态审查 -->
		<profile>
			<id>sonar</id>
			<activation>
				<activeByDefault>true</activeByDefault>
			</activation>
			<properties>
				<!-- Example for postgres -->
				<sonar.jdbc.url>jdbc:mysql://localhost:3306/sonar</sonar.jdbc.url>
				<sonar.jdbc.username>root</sonar.jdbc.username>
				<sonar.jdbc.password>root</sonar.jdbc.password>
				<!-- Optional URL to server. Default value is http://localhost:9000 -->
				<sonar.host.url>http://127.0.0.1:9000</sonar.host.url>
			</properties>
		</profile>

		<!-- 设置maven工程默认的jdk版本 -->
		<profile>
			<id>jdk-1.7</id>
			<activation>
				<activeByDefault>true</activeByDefault>
				<jdk>1.7</jdk>
			</activation>
			<properties>
				<maven.compiler.source>1.7</maven.compiler.source>
				<maven.compiler.target>1.7</maven.compiler.target>
				<maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
			</properties>
		</profile>

	</profiles>

	<!-- 激活可用的profile -->
	<activeProfiles>
		<activeProfile>adnet-sf</activeProfile>
	</activeProfiles>


</settings>

```

pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>ljx-testing-one</groupId>
  <artifactId>ljx-testing-one</artifactId>
  <version>0.0.8-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>ljx-testing-one</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>

		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.35</version>
		</dependency>

		<dependency>
		    <groupId>c3p0</groupId>
		    <artifactId>c3p0</artifactId>
		    <version>0.9.1.2</version>
		</dependency>
  </dependencies>

  <!-- deploy 权限 和 私服地址 配置 -->
  <distributionManagement>

    <!-- 指定snapshots快照版本仓库位置，其中id会与setting文件中的server id关联 -->
    <snapshotRepository>
      <id>adnet-ms</id>
      <url>http://172.16.1.25/nexus/content/repositories/snapshots</url>
    </snapshotRepository>

    <!-- 指定releases正式版本仓库位置，其中id会与setting文件中的server id关联 -->
    <repository>
      <id>adnet-ms</id>
      <url>http://172.16.1.25/nexus/content/repositories/releases</url>
    </repository>

  </distributionManagement>

</project>

```
