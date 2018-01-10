# antlr4-java③，生成文件解释

## 一、例子

以计算器的例子为基础。

g4文件：

```g4
prog:stat+;  
   
stat: expr NEWLINE       # printExpr  
| ID '=' expr NEWLINE    # assign  
| NEWLINE                # blank  
;  
expr:expr op=('*'|'/') expr     # MulDiv  
| expr op=('+'|'-') expr        # AddSub  
| INT                           # int  
| ID                            # id  
| '(' expr ')'                  # parens  
;  
ID : [a-zA-Z]+ ; // matchidentifiers  
INT : [0-9]+ ; // match integers  
NEWLINE:'\r'? '\n' ;//return newlinesto parser(end-statement signal)  
WS : [ \t]+ -> skip ;  
MUL : '*' ;  
DIV : '/' ;  
ADD : '+' ;  
SUB : '-' ; 
```

输入测试用例：

![image](http://picabstract.preview.ftn.qq.com:8080/ftn_pic_abs_v2/948375101d0d80272a917e7a0b7e87b190cb39bd8979f82d957a92e8cfae7fad6b5906826430454b2fece4913b24b863?pictype=scale&from=30012&version=2.0.0.2&uin=361376366&fname=antlr4-jisuanqi01.png&size=1024)

使用intellij idea插件看见的可视化语法树：

![image](http://picabstract.preview.ftn.qq.com:8080/ftn_pic_abs_v2/372226915ab8fce823131cfe7c6a0da5bf50ae76dbac9cb315d87e47e90cd9efb25edea0c74ba00f61579f278cc44cae?pictype=scale&from=30012&version=2.0.0.2&uin=361376366&fname=antlr4-jisuanqi02.png&size=1024)

## 二、g4文件

g4文件从概念上可以分为

- 词法【lexer】
- 语法【parser】

词法和语法结合组成树形结构。

- 词法是树中的节点
- 语法是将词法组合，从而形成树形（或树形一部分）

语法中可以设置 `# lable`，在生成java文件时，生成对应方法。在java中重写这些方法，实现我们自己想要的功能。

## 三、java文件

java文件分类：

- Listener---监听。当有事件发生时即可自动进行相关操作，强调自动。
- Visitor----访问。需要我们手动进行遍历，强调手动。

由语法中的`lable`生成的方法。

重写 `visit` 或重写 `visitXXX`，实现我们自己想要的功能。

```java
import org.antlr.v4.runtime.tree.ParseTreeVisitor;  
   
/** 
 * This interface defines a complete genericvisitor for a parse tree produced 
 * by {@linkLabeledExprParser}. 
 * 
 * @param<T> The return type of the visit operation. Use {@link Void} for 
 * operations with no return type. 
 */  
public interface LabeledExprVisitor<T>extends ParseTreeVisitor<T> {  
    T visitProg(LabeledExprParser.ProgContext ctx);  
    T visitPrintExpr(LabeledExprParser.PrintExprContext ctx);  
    T visitAssign(LabeledExprParser.AssignContext ctx);  
    T visitBlank(LabeledExprParser.BlankContext ctx);  
    T visitParens(LabeledExprParser.ParensContext ctx);  
    T visitMulDiv(LabeledExprParser.MulDivContext ctx);  
    T visitAddSub(LabeledExprParser.AddSubContext ctx);  
    T visitId(LabeledExprParser.IdContext ctx);  
    T visitInt(LabeledExprParser.IntContext ctx);  
}  
```

调用prog（这个方法名字也是我们自己在g4文件中定义的）方法访问了根节点，解析成tree，再调用visit方法，会调用所有的 `visitXXX`方法。

```java
import java.io.FileInputStream;
import java.io.InputStream;

import org.antlr.v4.runtime.ANTLRInputStream;
import org.antlr.v4.runtime.CommonTokenStream;
import org.antlr.v4.runtime.tree.ParseTree;

import com.sauzny.jkitchen_note.antlr.expr.LabeledExprLexer;
import com.sauzny.jkitchen_note.antlr.expr.LabeledExprParser;  
   
public class Main {  
    public static void main(String[] args) throws Exception {  
       // create a CharStream thatreads from standard input  
       String inputFile = System.getProperty("user.dir") + "/data.txt";  
       InputStream is = System.in;  
        
       if ( inputFile!=null ) is = new FileInputStream(inputFile);  
       ANTLRInputStream input = new ANTLRInputStream(is);  
        
       LabeledExprLexer lexer = new LabeledExprLexer(input);  
       CommonTokenStream tokens = new CommonTokenStream(lexer);  
       LabeledExprParser parser = new LabeledExprParser(tokens);  
       ParseTree tree = parser.prog(); // parse  
       EvalVisitor eval = new EvalVisitor();  
       eval.visit(tree);  
    }  
}  
```