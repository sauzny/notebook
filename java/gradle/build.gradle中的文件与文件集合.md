# build.gradle中的操作文件

## 一、 定位文件

```groovy
// 使用一个相对路径

File configFile = file('src/config.xml')

// 使用一个绝对路径

configFile = file(configFile.absolutePath)

// 使用一个项目路径的文件对象 

configFile = file(new File('src/config.xml'))`
```

## 二、创建文件集合

```groovy
FileCollection collection = files('src/file1.txt',
                                  new File('src/file2.txt'),
                                  ['src/file3.txt', 'src/file4.txt'])
```

## 三、使用文件集合

```groovy
// 对文件集合进行迭代
collection.each {File file ->
    println file.name
}

// 转换文件集合为其他类型
Set set = collection.files
Set set2 = collection as Set
List list = collection as List
String path = collection.asPath
File file = collection.singleFile
File file2 = collection as File

// 增加和减少文件集合
def union = collection + files('src/file3.txt')
def different = collection - files('src/file3.txt')
```

## 四、实现一个文件集合

```groovy
task list << {
    File srcDir

    // 使用闭合创建一个文件集合
    collection = files { srcDir.listFiles() }

    srcDir = file('src')
    println "Contents of $srcDir.name"
    collection.collect { relativePath(it) }.sort().each { println it }

    srcDir = file('src2')
    println "Contents of $srcDir.name"
    collection.collect { relativePath(it) }.sort().each { println it }
}
```

输出结果

```groovy
> gradle -q list
Contents of src
src/dir1
src/file1.txt
Contents of src2
src2/dir1
src2/dir2
```