# java 安装配置环境变量

## windows

- 1.打开我的电脑--属性--高级--环境变量 

- 2.新建系统变量JAVA_HOME 和CLASSPATH 
    - 变量名：JAVA_HOME 
    - 变量值：C:\Program Files\Java\jdk1.7.0
    - 变量名：CLASSPATH 
    - 变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;

- 3.选择“系统变量”中变量名为“Path”的环境变量，双击该变量，把JDK安装路径中bin目录的绝对路径，添加到Path变量的值中，并使用半角的分号和已有的路径进行分隔。 
    - 变量名：Path 
    - 变量值：%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
    

## linux

### 1. 永久修改，对所有用户有效

```
# vi /etc/profile
```

按键盘`Shift + g`, 在profile文件最后添加下面的内容:

```
export JAVA_HOME = /home/myuser/jdk1.7.0_03
export PATH = $JAVA_HOME/bin:$PATH

export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

==[注]==：修改文件后如果想马上生效，还要运行 #source /etc/profile

 

### 2. 永久修改，对单一用户有效

修改用户目录下的.bash_profile文件

```
$ vi /home/myuser/.bash_profile
```

在文件最后添加下面的内容

```
export JAVA_HOME = /home/myuser/jdk1.7.0_03
export PATH = $JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

==[注]==：修改文件后如果想马上生效，还要运行 `#source /home/myuser/.bash_profile`

### 3. 只对当前bash /shell 生效

```
$ export JAVA_HOME = /home/myuser/jdk1.7.0_03
$ export PATH = $JAVA_HOME/bin:$PATH
$ export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```