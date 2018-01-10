# java代码规范_v0.0.2

本文内容改编自：http://www.hawstein.com/posts/google-java-style.html

### `请秉承着易读性和代码即注释的心态编写代码！！！`
### `代码是用来奔跑的，也是用来让别人和你一起奔跑的！！！`

### 1 import不要使用通配符
不要出现类似这样的import语句：`import java.util.*;`，每个import语句独立成行。

### 2 类成员顺序
类的成员顺序对易读性有很大的影响，但这也不存在唯一的通用法则。不同的类对成员的排序可能是不同的。 最重要的一点，每个类应该以某种逻辑去排序它的成员，维护者应该要能解释这种排序逻辑。比如， 新的方法不能总是习惯性地添加到类的结尾，因为这样就是按时间顺序而非某种逻辑来排序的。

### 3 重载：永不分离

当一个类有多个构造函数，或是多个同名方法，这些函数/方法应该按顺序出现在一起，中间不要放进其它函数/方法。


### 4 使用大括号(即使是语法上可以不写)
大括号与if, else, for, do, while语句一起使用，即使只有一条语句(或是空)，也应该把大括号写上。

### 5 非空块：K & R 风格

对于非空块和块状结构，大括号遵循Kernighan和Ritchie风格 (Egyptian brackets):

- 左大括号前不换行
- 左大括号后换行
- 右大括号前换行
- 如果右大括号是一个语句、函数体或类的终止，则右大括号后换行; 否则不换行。例如，如果右大括号后面是else或逗号，则不换行。

示例：

    return new MyClass() {
      @Override public void method() {
        if (condition()) {
          try {
            something();
          } catch (ProblemException e) {
            recover();
          }
        }
      }
    };

### 6 空块：可以用简洁版本

一个空的块状结构里什么也不包含，大括号可以简洁地写成{}，不需要换行。例外：如果它是一个多块语句的一部分(if/else 或 try/catch/finally) ，即使大括号内没内容，右大括号也要换行。

示例：

    void doNothing() {}


### 7 一行一个语句

每个语句后要换行。每行字符数不超过100个，换行请尽量保持易读性。

### 8 命名规范

#### 8.1 包名

包名全部小写，连续的单词只是简单地连接起来，不使用下划线。


#### 8.2 类名

驼峰规则。

类名通常以名词结尾。而且在类名中要体现它是以保存数据为主还是提供功能为主。例如 ConnectionBuilder 这个类我们都可以猜到它的主要功能是创建 Connection 对象，

以动词-er/or 结尾的类名，至少应该包含一个以该动词开头的方法。例如 ConnectionBuilder 这个类，它至少应该包含一个以 build- 开头的方法。有了这种默契，别人就能更方便的使用这个类。

测试类的命名以它要测试的类的名称开始，以Test结束。例如，HashTest或HashIntegrationTest。

#### 8.3 方法名

驼峰规则。

方法名通常是动词或动词短语。

- **增删改查**
以对 Person 类的增删改查为例——
创建一个 Person对象的方法一般命名为 createPerson()/newPerson()，我们使用createPerson()
添加方法命名为 addPerson()，修改方法命名为 updatePerson()，
删除方法命名为 deletePerson()/removePerson()，我们使用deletePerson()
根据主键查询的方法命名为 getPerson()/queryPerson()。我们使用queryPerson()
根据其他条件来查询，要指出具体的条件类型，例如 getPersonByName() 或 getPersonByAge()。

- **计数方法**
计数方法通常命名为 getNumberOfXXX()/getXXXCount()。例如 getNumberOfRows()，getQuestionCount()。我们使用getQuestionCount()

- **boolean方法**
在 JavaBean 中，返回 boolean 属性必须用 isXXX() 命名。

- **检查判空**
检查某个属性是否为空或者某条记录是否存在的方法，通常命名为 hasXXX()，例如 hasResult()。

- **检查状态**
检查对象状态的方法通常以 is+形容词 命名。例如 isClosed()，isReady()

- **返回集合对象的方法**
返回集合对象的方法应该体现出复数形式，例如 getPersons()，也可以体现容器类型，例如 getPersonList()。

- **不要简化单词**
不要写 getHisList()，而要写 getHistoryList()；不要写 usrno，而要写 userNumber。一个简单的原则是要能够把这个名字读出来。

- **参数命名应该精确**
createUser(String str1, String str2) 看起来很难懂，但要是写成 createUser(String userName, String password) 就很好懂了。

- **返回值命名为 result**
在一个方法中将返回值命名为 result，能够让方法的脉络更清晰。

- **单元测试**
下划线可能出现在JUnit测试方法名称中用以分隔名称的逻辑组件。测试同一个方法，不同的结果或者状态，使用下划线分割。一个典型的模式是：test<MethodUnderTest>_<state>，例如testPop_emptyStack。 并不存在唯一正确的方式来命名测试方法。


#### 8.4 常量名

常量名命名模式为CONSTANT_CASE，全部字母大写，用下划线分隔单词。那，到底什么算是一个常量？

每个常量都是一个静态final字段，但不是所有静态final字段都是常量。在决定一个字段是否是一个常量时， 考虑它是否真的感觉像是一个常量。例如，如果任何一个该实例的观测状态是可变的，则它几乎肯定不会是一个常量。 只是永远不打算改变对象一般是不够的，它要真的一直不变才能将它示为常量。


#### 8.5 非常量字段名

驼峰规则。

- **集合类对象变量命名**
使用 `xxxList` `xxxSet` `xxxMap` 这样的方式命名

- **集合类对象变量命名**
使用 `xxxArray` 这样的方式命名

#### 8.6 参数名

驼峰规则。


#### 8.7 局部变量名

驼峰规则。

#### 8.8 特殊命名（触角位置命名）

触角位置：与其他各种语言服务或存储介质（`DB，Cache，xml，yml，properties`等等）交互的实体类，属于一个程序中最外层。
由于触角位置会接触到不同类型的编程语言，不同的编码风格，所以此处的命名没有强制规范，秉承着易读性编码即可。

### 9 @Override：能用则用

只要是合法的，就把@Override注解给用上。


### 10 捕获的异常：不能忽视

一般来说，捕获到的异常都是需要自己处理的（再次抛出），处理之后还要携带原始的异常信息。不能只是简单的打印出堆栈信息。

### 11 行注释

注释尽量换行使用，代码可以直接追加行注释

### 12 方法注释

每一个方法都应该有一个javadoc的方法注释（setter，getter不必要）

### 13 方法内部逻辑注释
-

### 14 工具类

以Utils结尾，注意结尾有s。一般工具类，所有方法都是static，不需要创建实例，使用final修饰，私有化其构造方法。

    public final class MyStringUtils {

        private MyStringUtils(){}
    }

### 15 pom文件中的标签

pom文件中的标签，需要增加注释，注释的颜色不同，方便查看。同类型（如log会使用多个标签）的标签尽量放在一起，不要按照增加的时间顺序摆放这些标签。


### 16 model转换工具类

命名规范 xxx2Xxxx()


