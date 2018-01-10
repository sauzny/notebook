# antlr4④，g4文件语法规则

g4文件从概念上可以分为

- 词法【lexer】
- 语法【parser】

词法文件和语法文件可以分开写，也可以合并写在一个文件中。

## g4文件的结构

完整的antlr语法规则文件:

```g4
grammar Name;
options {...}
import ...;
tokens {...}
@actionName {...}
<<rule1>>
...
<<rule2>>
```

- **grammar Name** 这是词法跟语法都在同一个文件声明的写法，称之为combined。若要分开，可以使用
     - lexer grammar Name
     - parser grammar Name。

- **options** 可以是如下四个选项。
    - **superClass**：用于生成xxxLexer.java、xxxParser.java的父类
    - **language**：目标语句，如java
    - **tokenVocab**：toekn词库
    - **TokenLabelType**：默认的是antlr的Token类型，这里可以使用自定义的token类，如MyToken。需要配合TokenFactory使用

- **import** 可以导入各个独立的lexer、parser文件，只能用于combined写法。
- **actionName** 可以是如下内容

    - **@header**：定义类文件头。比如嵌入java的package、import声明
    - **@member**：定义类文件内容。比如类成员、方法

    如果要指定在lexer或者parser中，可以使用 @lexer::membere、@parser::member。

- **rule** 语法规则。

    - **lexer示例**
    ```
    lexer grammar HelloLexer;
    HI : 'H' 'i'
    ID : [a-z]+;
    WS : [\t\n\r\s]+ -> skip;
    ```

    - **parser示例**
    ```
    parser grammar HelloParser;
    options {
        language=Java;
        tokenVocab=HelloLexer;
    }
    
    @header {
        package com.sauzny.jkitchen_note.antlr.expr;
        import java.util.Set;
        import java.util.HashSet;
    }
    
    @member {
        private int count;
        public int getCount() {
            return count;
        }
    }
    
    start : HI ID;
    ```
