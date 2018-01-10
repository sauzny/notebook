# build.gradle中的task操作文件

## 一、复制文件

```groovy
task copyTask(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
}
```

```groovy
task anotherCopyTask(type: Copy) {
    // 复制 src/main/webapp 目录下的所有文件
    from 'src/main/webapp'
    // 复制一个单独文件
    from 'src/staging/index.html'
    // 复制一个任务输出的文件
    from copyTask
    // 显式使用任务的 outputs 属性复制任务的输出文件
    from copyTaskWithPatterns.outputs
    // 复制一个 ZIP 压缩文件的内容
    from zipTree('src/main/assets.zip')
    // 最后指定目标目录
    into { getDestDir() }
}
```

**选择要复制文件**

```groovy
task copyTaskWithPatterns(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
    include '**/*.html'
    include '**/*.jsp'
    exclude { details -> details.file.name.endsWith('.html') &&
                         details.file.text.contains('staging') }
}
```

**不使用最新检查方式下用 copy() 方法复制文件**

```groovy
task copyMethod << {
    copy {
        from 'src/main/webapp'
        into 'build/explodedWar'
        include '**/*.html'
        include '**/*.jsp'
    }
}
```

**使用最新的检查方式下用 copy() 方法复制文件**
```groovy
task copyMethodWithExplicitDependencies{

     // 对输入做最新检查,添加 copyTask 作为依赖
    inputs.file copyTask
    outputs.dir 'some-dir' //对输出做最新检查
    doLast{
        copy {
            // 复制 copyTask 的输出
            from copyTask
            into 'some-dir'
        }
    }
}
```

**在复制时重命名文件**
```groovy
task rename(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
    // 使用一个闭合映射文件名
    rename { String fileName ->
        fileName.replace('-staging-', '')
    }
    // 使用正则表达式映射文件名
    rename '(.+)-staging-(.+)', '$1$2'
    rename(/(.+)-staging-(.+)/, '$1$2')
}
```

**在复制时过滤文件**
```groovy
import org.apache.tools.ant.filters.FixCrLfFilter
import org.apache.tools.ant.filters.ReplaceTokens

task filter(type: Copy) {
    from 'src/main/webapp'
    into 'build/explodedWar'
    // 在文件中替代属性标记
    expand(copyright: '2009', version: '2.3.1')
    expand(project.properties)
    // 使用 Ant 提供的过滤器
    filter(FixCrLfFilter)
    filter(ReplaceTokens, tokens: [copyright: '2009', version: '2.3.1'])
    // 用一个闭合来过滤每一行
    filter { String line ->
        "[$line]"
    }
    // 使用闭合来删除行
    filter { String line ->
        line.startsWith('-') ? null : line
    }
}
```

**嵌套复制规范**
```groovy
task nestedSpecs(type: Copy) {
    into 'build/explodedWar'
    exclude '**/*staging*'
    from('src/dist') {
        include '**/*.html'
    }
    into('libs') {
        from configurations.runtime
    }
}
```

## 二、使用同步任务

同步任务 ( Sync ) 任务继承自复制任务 ( Copy ) 

当它执行时,它会复制源文件到目标目录中,然后从目标目录中的删除所有非复制的文件

这种方式非常有用,比如安装一个应用,创建一个文档的副本,或者维护项目的依赖关系副本.

```groovy
task libs(type: Sync) {
    from configurations.runtime
    into "$buildDir/libs"
}
```

## 三、创建归档文件

**创建一个 ZIP 文档**
```groovy
apply plugin: 'java'

task zip(type: Zip) {
    from 'src/dist'
    into('libs') {
        from configurations.runtime
    }
}
```

**创建压缩文档**
```groovy
apply plugin: 'java'

version = 1.0

task myZip(type: Zip) {
    from 'somedir'
}

println myZip.archiveName
println relativePath(myZip.destinationDir)
println relativePath(myZip.archivePath)
```

使用 gradle -q myZip 命令进行输出:

```groovy
> gradle -q myZip
zipProject-1.0.zip
build/distributions
build/distributions/zipProject-1.0.zip
```

**归档任务-属性名**

属性名 | 类型 | 默认值 | 描述
--- | --- | --- | ---
archiveName | 字符 | baseName-appendix-version-classifier.extension,如果其中任何一个都是空的,则不添加名称 | 归档文件的基本文件名
archivePath | 文件 | destinationDir/archiveName | 生成归档文件的绝对路径。
destinationDir | 文件 | 取决于文档类型, JAR 和 WAR 使用project.buildDir/distributions. project.buildDir/libraries.ZIP 和 TAR | 归档文件的目录
baseName | 字符 | project.name | 归档文件名的基础部分。
appendix | 字符 | null | 归档文件名的附加部分。
version | 字符 | project.version | 归档文件名的版本部分。
classifier | 字符 | null | 归档文件名的分类部分
extension | 字符 | 取决于文档类型和压缩类型: zip, jar, war, tar, tgz 或者 tbz2. | 归档文件的扩展名

**配置归档文件-自定义文档名**
```groovy
apply plugin: 'java'
version = 1.0

task myZip(type: Zip) {
    from 'somedir'
    baseName = 'customName'
}

println myZip.archiveName
```

使用 gradle -q myZip 命令进行输出:

```groovy
> gradle -q myZip
customName-1.0.zip
```

**配置归档任务- 附加其他后缀**
```groovy
apply plugin: 'java'
archivesBaseName = 'gradle'
version = 1.0

task myZip(type: Zip) {
    appendix = 'wrapper'
    classifier = 'src'
    from 'somedir'
}

println myZip.archiveName
```

使用 gradle -q myZip 命令进行输出:

```groovy
> gradle -q myZip
gradle-wrapper-1.0-src.zip
```