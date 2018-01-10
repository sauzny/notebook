# antlr4⑥，g4文件词法匹配规则

## 举例一、

==规则： 若输入串能被多个词法规则匹配，那么声明在词法文件最前面的规则生效。==

语法文件：parser
```g4
parser grammar HelloParser;
options {
    language=Java;
    tokenVocab=HelloLexer;
}

start : HI ID;
```

词法文件1：lexer1
```g4
lexer grammar HelloLexer;   //Definea grammar called Hello
ID : [a-zA-Z]+;
HI : 'H' 'I';
WS : [\t\r\n ]+->skip;  //skip spaces,tabs,newlines,\r(Windows)
```

词法文件2：lexer2
```g4
lexer grammar HelloLexer;   //Definea grammar called Hello
HI : 'H' 'I';
ID : [a-zA-Z]+;
WS : [\t\r\n ]+->skip;  //skip spaces,tabs,newlines,\r(Windows)
```

测试输入：HI antlr

结果：

- parser + lexer1，匹配失败。原因：输入串`HI`，被词法`ID`优先匹配，`antlr`也被`ID`词法匹配，所以无法匹配语法规则`start : HI ID;`
- parser + lexer2，匹配成功。输入串`HI`，被词法`HI`优先匹配，`antlr`被`ID`词法匹配。


## 举例二、

==规则： 输入串将被最长匹配的词法规则匹配。==

比如ABCD肯定会被能完全匹配ABCD的词法规则匹配，而不是将ABCD拆开分别被两个词法ABC、D匹配。

语法文件：parser
```g4
parser grammar HelloParser;
options {
    language=Java;
    tokenVocab=HelloLexer;
}
numeric_literal : INTEGER | NUMERIC;
```

词法文件：lexer
```g4
//lexer
lexer grammar HelloLexer;   //Definea grammar called Hello
ZERO : '0';
DOT : '.';
UNDERLINE : '_';
HI : 'H' 'I';

fragment
    DIGIT : [0-9];

INTEGER : ZERO|[1-9] DIGIT*;
NUMERIC : INTEGER DOT DIGIT+;
ALPHABET : [a-zA-Z];
ID : (ALPHABET|UNDERLINE) (DIGIT|ALPHABET|UNDERLINE)+;
WS : [\t\r\n ]+->skip;  //skip spaces,tabs,newlines,\r(Windows)
```

对于输入串`12.03`，只会被`NUMERIC`词法匹配，而不会被拆成`12`、`.`、`0`、`3`分别被`INTEGER`、`DOT`、`INTEGER`、`INTEGER`匹配。

这就是最长匹配原则，也叫贪婪匹配。

## 举例三、

目标语言对大小写是否敏感

**大小写敏**感 对26个英文字母分别定义大写、小写两个词法规则。使用的时候大写地方就用大写的词法规则，小写就用小写的词法规则。

```
fragment
    A : [A];
fragment
    A_ : [a];
```

**大小写不敏感** 对26个字母定义一个词法规则，包含大小写字母在里面。使用的时候，直接使用词法规则代替。

```    
fragment
    A : [aA];
```