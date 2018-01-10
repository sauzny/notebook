# antlr4-java②，编写简单计算器

## 一、编写简单计算器g4文件

```
grammar LabeledExpr;  
   
/** The start rule; beginparsing here. */  
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

## 二、测试使用

新建maven项目，pom.xml中增加jar包

```
<dependency>
    <groupId>org.antlr</groupId>
    <artifactId>antlr4-runtime</artifactId>
    <version>4.7</version>
</dependency>
```

把生成好的java文件和tokens文件copy到项目中。

编写两个java文件

```java
import java.util.HashMap;
import java.util.Map;

import com.sauzny.jkitchen_note.antlr.expr.LabeledExprBaseVisitor;
import com.sauzny.jkitchen_note.antlr.expr.LabeledExprParser;

public class EvalVisitor extends LabeledExprBaseVisitor<Integer> {
    
    Map<String, Integer> memory = new HashMap<String, Integer>();

    @Override  
    public Integer visitPrintExpr(LabeledExprParser.PrintExprContext ctx) {  
       // TODO Auto-generatedmethod stub  
       Integer value = visit(ctx.expr()); // evaluate the exprchild  
       System.out.println(value); // print theresult  
       return 0; // return dummyvalue  
    }

    @Override
    public Integer visitAssign(LabeledExprParser.AssignContext ctx) {
        // TODO Auto-generatedmethod stub
        String id = ctx.ID().getText(); // id is left-hand side of '='
        int value = visit(ctx.expr()); // compute valueof expression on right
        memory.put(id, value); // store it inour memory
        return value;
    }

    @Override
    public Integer visitBlank(LabeledExprParser.BlankContext ctx) {
        // TODO Auto-generatedmethod stub
        return super.visitBlank(ctx);
    }

    @Override
    public Integer visitParens(LabeledExprParser.ParensContext ctx) {
        // TODO Auto-generatedmethod stub
        return visit(ctx.expr()); // return childexpr's value
    }

    @Override
    public Integer visitMulDiv(LabeledExprParser.MulDivContext ctx) {
        // TODO Auto-generatedmethod stub
        int left = visit(ctx.expr(0)); // get value ofleft subexpression
        int right = visit(ctx.expr(1)); // get value ofright subexpression
        if (ctx.op.getType() == LabeledExprParser.MUL)
            return left * right;
        return left / right; // must be DIV
    }

    @Override
    public Integer visitAddSub(LabeledExprParser.AddSubContext ctx) {
        // TODO Auto-generatedmethod stub
        int left = visit(ctx.expr(0)); // get value ofleft subexpression
        int right = visit(ctx.expr(1)); // get value ofright subexpression
        if (ctx.op.getType() == LabeledExprParser.ADD)
            return left + right;
        return left - right; // must be SUB
    }

    @Override
    public Integer visitId(LabeledExprParser.IdContext ctx) {
        // TODO Auto-generatedmethod stub
        String id = ctx.ID().getText();
        if (memory.containsKey(id))
            return memory.get(id);
        return 0;
    }

    @Override
    public Integer visitInt(LabeledExprParser.IntContext ctx) {
        // TODO Auto-generatedmethod stub
        return Integer.valueOf(ctx.INT().getText());
    }
}

```

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

编写测试文件data.txt，放在此项目根目录下

```txt
3*5+3
(3+4)*4/5
a=1
b=2
a+b*3
```

运行Main这个类，可以查看结。