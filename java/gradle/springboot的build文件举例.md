# springboot的build文件举例

在项目所在更目录下，build命令，打包、跳过测试、执行task copyJars

`gradle clean build -x test copyJars copyConfs`

打包之后的问价目录结构

```
project
    └─ build
        ├─ classes
        ├─ libs
        │    ├─ config  // 配置文件，xx.properties，xx.xml
        │    ├─ lib     // 依赖的 jar 包
        │    └─ xxxx.jar
        └─ tmp
```

在项目所在更目录下，java启动命令

`java -jar build/libs/xxx.jar`


build文件：

```java
 
buildscript {
	ext {
	    springVersion = "4.3.12.RELEASE"
	    junitVersion = "4.12"
	    poiVersion = "3.14"
	    jacksonVersion = "2.8.10"
	    springBootVersion = '1.5.8.RELEASE'
	    springfoxVersion = '2.7.0'
	}

	repositories {
		mavenCentral()
		maven { url "http://172.16.1.21:9622/nexus/content/groups/public" }
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:$springBootVersion")
	}
}

apply plugin: "java"
apply plugin: "eclipse"
// apply plugin: "org.springframework.boot"


group "xxxxxxx"
version "1.0-SNAPSHOT"
sourceCompatibility=1.8
targetCompatibility =1.8
[compileJava, compileTestJava, javadoc]*.options*.encoding = 'UTF-8'

repositories {
    mavenLocal()
    maven { url "http://172.16.1.21:9622/nexus/content/groups/public" }
}



dependencies {

	// 添加依赖时同一个group下的多个包，版本号要定义变量标识。单个包可以直接使用版本号
    compile (
		"org.apache.poi:poi:$poiVersion",
		"org.apache.poi:poi-ooxml:$poiVersion",
        "org.springframework:spring-core:$springVersion",
        "org.springframework:spring-context:$springVersion",
        "org.springframework:spring-context-support:$springVersion",
        "org.springframework:spring-jdbc:$springVersion",
		"org.springframework.boot:spring-boot-starter-web:$springBootVersion",        
        "org.springframework.boot:spring-boot-starter-jooq:$springBootVersion",     
        "org.springframework.boot:spring-boot-starter-aop:$springBootVersion",
        "io.springfox:springfox-swagger2:$springfoxVersion",
        "io.springfox:springfox-swagger-ui:$springfoxVersion",    
		"com.fasterxml.jackson.module:jackson-module-jaxb-annotations:$jacksonVersion",   
		"com.fasterxml.jackson.datatype:jackson-datatype-jsr310:$jacksonVersion",            
        
		"org.antlr:antlr4-runtime:4.7",
		"mysql:mysql-connector-java:5.1.34",        
		"com.h2database:h2:1.4.196",
        "com.alibaba:druid:1.1.4",        
        "org.mongodb:mongo-java-driver:1.3",
		"commons-beanutils:commons-beanutils:1.9.3",
		"commons-codec:commons-codec:1.11",
		"commons-io:commons-io:2.6",
		"com.google.guava:guava:23.0",
		"org.apache.commons:commons-lang3:3.6",
		"org.apache.commons:commons-text:1.1"		    
    )
    runtime(
    	"org.springframework.boot:spring-boot-devtools:$springBootVersion"
    )

    testCompile (
        "junit:junit:$junitVersion",
        "org.springframework:spring-test:$springVersion",
        "org.springframework.boot:spring-boot-starter-test:$springBootVersion"
    )
}
tasks.withType(JavaCompile) {
	options.encoding = "UTF-8"
}

// 将项目运行时依赖的包放在指定目录
task copyJars(type: Copy) {
  from configurations.runtime
  into "$buildDir/libs/lib" // 目标位置
}

// 将配置文件copy到指定目录
task copyConfs(type: Copy){
    from 'src/main/resources'
    into "$buildDir/libs/config"
}

jar {
    manifest {
    	attributes(
			"Implementation-Title": "Gradle",
      	
        	"Manifest-Version": 1.0,
        	// 设置main函数所在的类
        	"Main-Class": "xxx.xxx.xxx.Demo",
        	// 将配置文件所在的目录和项目运行时依赖的包 写入文件MANIFEST.MF中
        	"Class-Path": "config/ " + configurations.compile.collect { "lib/${it.name}" }.join(' ')
        	)
    }
}

// 排除配置文件
// 重写 compileJava 时执行的 processResources 方法
processResources {
    exclude { "**/*.*" }
}

```