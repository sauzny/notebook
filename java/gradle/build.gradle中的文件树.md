# build.gradle中的文件树

文件树就是一个按照层次结构分布的文件集合

例如,一个文件树可以代表一个目录树结构或者一个 ZIP 压缩文件的内容.

它被抽象为 FileTree 结构,FileTree 继承自 FileCollection

所以你可以像处理文件集合一样处理文件树,

**Gradle 有些对象实现了FileTree 接口**

例如 源集合. 使用 Project.fileTree() 方法可以得到 FileTree 的实例,它会创建一个基于基准目录的对象,然后视需要使用一些 Ant-style 的包含和去除规则.

## 一、 创建文件树 build.gradle

```groovy
/以一个基准目录创建一个文件树
FileTree tree = fileTree(dir: 'src/main')

// 添加包含和排除规则
tree.include '**/*.java'
tree.exclude '**/Abstract*'

// 使用路径创建一个树
tree = fileTree('src').include('**/*.java')

// 使用闭合创建一个数
tree = fileTree('src') {
    include '**/*.java'
}

// 使用map创建一个树
tree = fileTree(dir: 'src', include: '**/*.java')
tree = fileTree(dir: 'src', includes: ['**/*.java', '**/*.xml'])
tree = fileTree(dir: 'src', include: '**/*.java', exclude: '**/*test*/**')
```

## 二、使用文件树 build.gradle

```groovy
// 遍历文件树
tree.each {File file ->
    println file
}

// 过滤文件树
FileTree filtered = tree.matching {
    include 'org/gradle/api/**'
}

// 合并文件树A
FileTree sum = tree + fileTree(dir: 'src/test')

// 访问文件数的元素
tree.visit {element ->
    println "$element.relativePath => $element.file"
}
```

## 三、使用压缩文档作为文件树 build.gradle

```groovy
// 使用路径创建一个 ZIP 文件
FileTree zip = zipTree('someFile.zip')

// 使用路径创建一个 TAR 文件
FileTree tar = tarTree('someFile.tar')

//tar tree 能够根据文件扩展名得到压缩方式,如果你想明确的指定压缩方式,你可以使用下面方法
FileTree someTar = tarTree(resources.gzip('someTar.ext'))
```