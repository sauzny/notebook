# antlr4⑤，g4编写词法定义

词法文件的开头应该就是根据是否大小敏感先把26个英文字母的词法规则确定。

然后就是定义关键字及预定义字符。关键字就是目标语言的保留、非保留关键字。

- 保留关键字是指只允许在语言规范定义的地方使用，不能在其他地方使用
- 非保留关键字是指可以使用，但不推荐使用。
- 预定义字符是指诸如操作符(>, <, =, >>, >>>, >=, <=, ., ^, %, $, @, !, *, +, -等)。

紧接着就可以定义其他fragment了，如DIGIT。fragment就是定义一些不需要被识别为独立token的字符，如0-9，定义一个fragment给其他词法规则引用，而0-9本身可以另外定义一个整数的词法规则来匹配。

一般会将fragment、单字符定义、关键字定义放在最前面

而具体的词法（token）定义放在后面。

- **fragment** 词法片段，构成词法的元素，不是一个词法规则。在词法规则中可引用一个或多个词法片段。如：

```
fragement
    DIGIT : [0-9];
INTEGER : DIGIT | [1-9] DIGIT+;
```

- **{action}** 词法规则关联的目标语言代码。当输入串被词法规则匹配时，执行定义的action。如：

```
fragement
    DIGIT : [0-9];
INTEGER : DIGIT | [1-9] DIGIT+ {System.out.println("match an integer.");}; 
```

- **mode** 主要用于解决一个语法中包含一个或多个其他不同的语法。如Java语法的annotation（注解）。再比如XML文件，示例如下：
                              
```
lexer grammar XMLLexer;
// Default "mode" : Everything OUTSIDE of a tag
OPEN : '<' ->pushMode(INSIDE);
COMMENT : '<!--'.*?'-->' ->skip;
EntityRef : '&'[a-z]+';';
TEXT : ~('<'|'&')+; //match any 16bit char minus < and &
// -----------------Everything INSIDE of a tag---------------------

mode INSIDE;
CLOSE : '>' ->popMode;//back to default mode
SLASH_CLOSE: '/>' ->popMode;
EQUALS : '=';
STRING : '"'.*?'"';
SlashName : '/'Name;
Name : ALPHA (ALPHA|DIGIT)*;
S : [\t\r\n] ->skip;
fragment
    ALPHA : [a-zA-Z];
fragment
    DIGIT : [0-9];
```

- **hidden channel** 将不需要关注的如注释、空格等发送到隐藏通道中。当然需要的时候还可以用antlr的api获取回来。如：

```
COMMENT : '/*'.*?'*/' -> channel(HIDDEN); //match anything between /* and */
WS : [\r\t\u000C\n]+ -> channel(HIDDEN);
```

sqlite的示例：

```g4
fragment DIGIT : [0-9];
fragment A : [aA];
fragment B : [bB];
fragment C : [cC];
fragment D : [dD];
fragment E : [eE];
fragment F : [fF];
fragment G : [gG];
fragment H : [hH];
fragment I : [iI];
fragment J : [jJ];
fragment K : [kK];
fragment L : [lL];
fragment M : [mM];
fragment N : [nN];
fragment O : [oO];
fragment P : [pP];
fragment Q : [qQ];
fragment R : [rR];
fragment S : [sS];
fragment T : [tT];
fragment U : [uU];
fragment V : [vV];
fragment W : [wW];
fragment X : [xX];
fragment Y : [yY];
fragment Z : [zZ];

SCOL : ';';
DOT : '.';
OPEN_PAR : '(';
CLOSE_PAR : ')';
COMMA : ',';
ASSIGN : '=';
STAR : '*';
PLUS : '+';
MINUS : '-';
TILDE : '~';
PIPE2 : '||';
DIV : '/';
MOD : '%';
LT2 : '<<';
GT2 : '>>';
AMP : '&';
PIPE : '|';
LT : '<';
LT_EQ : '<=';
GT : '>';
GT_EQ : '>=';
EQ : '==';
NOT_EQ1 : '!=';
NOT_EQ2 : '<>';

// http://www.sqlite.org/lang_keywords.html
K_ABORT : A B O R T;
K_ACTION : A C T I O N;
K_ADD : A D D;
K_AFTER : A F T E R;
K_ALL : A L L;
K_ALTER : A L T E R;
K_ANALYZE : A N A L Y Z E;
K_AND : A N D;
K_AS : A S;
K_ASC : A S C;
K_ATTACH : A T T A C H;
K_AUTOINCREMENT : A U T O I N C R E M E N T;
K_BEFORE : B E F O R E;
K_BEGIN : B E G I N;
K_BETWEEN : B E T W E E N;
K_BY : B Y;
K_CASCADE : C A S C A D E;
K_CASE : C A S E;
K_CAST : C A S T;
K_CHECK : C H E C K;
K_COLLATE : C O L L A T E;
K_COLUMN : C O L U M N;
K_COMMIT : C O M M I T;
K_CONFLICT : C O N F L I C T;
K_CONSTRAINT : C O N S T R A I N T;
K_CREATE : C R E A T E;
K_CROSS : C R O S S;
K_CURRENT_DATE : C U R R E N T '_' D A T E;
K_CURRENT_TIME : C U R R E N T '_' T I M E;
K_CURRENT_TIMESTAMP : C U R R E N T '_' T I M E S T A M P;
K_DATABASE : D A T A B A S E;
K_DEFAULT : D E F A U L T;
K_DEFERRABLE : D E F E R R A B L E;
K_DEFERRED : D E F E R R E D;
K_DELETE : D E L E T E;
K_DESC : D E S C;
K_DETACH : D E T A C H;
K_DISTINCT : D I S T I N C T;
K_DROP : D R O P;
K_EACH : E A C H;
K_ELSE : E L S E;
K_END : E N D;
K_ESCAPE : E S C A P E;
K_EXCEPT : E X C E P T;
K_EXCLUSIVE : E X C L U S I V E;
K_EXISTS : E X I S T S;
K_EXPLAIN : E X P L A I N;
K_FAIL : F A I L;
K_FOR : F O R;
K_FOREIGN : F O R E I G N;
K_FROM : F R O M;
K_FULL : F U L L;
K_GLOB : G L O B;
K_GROUP : G R O U P;
K_HAVING : H A V I N G;
K_IF : I F;
K_IGNORE : I G N O R E;
K_IMMEDIATE : I M M E D I A T E;
K_IN : I N;
K_INDEX : I N D E X;
K_INDEXED : I N D E X E D;
K_INITIALLY : I N I T I A L L Y;
K_INNER : I N N E R;
K_INSERT : I N S E R T;
K_INSTEAD : I N S T E A D;
K_INTERSECT : I N T E R S E C T;
K_INTO : I N T O;
K_IS : I S;
K_ISNULL : I S N U L L;
K_JOIN : J O I N;
K_KEY : K E Y;
K_LEFT : L E F T;
K_LIKE : L I K E;
K_LIMIT : L I M I T;
K_MATCH : M A T C H;
K_NATURAL : N A T U R A L;
K_NO : N O;
K_NOT : N O T;
K_NOTNULL : N O T N U L L;
K_NULL : N U L L;
K_OF : O F;
K_OFFSET : O F F S E T;
K_ON : O N;
K_OR : O R;
K_ORDER : O R D E R;
K_OUTER : O U T E R;
K_PLAN : P L A N;
K_PRAGMA : P R A G M A;
K_PRIMARY : P R I M A R Y;
K_QUERY : Q U E R Y;
K_RAISE : R A I S E;
K_RECURSIVE : R E C U R S I V E;
K_REFERENCES : R E F E R E N C E S;
K_REGEXP : R E G E X P;
K_REINDEX : R E I N D E X;
K_RELEASE : R E L E A S E;
K_RENAME : R E N A M E;
K_REPLACE : R E P L A C E;
K_RESTRICT : R E S T R I C T;
K_RIGHT : R I G H T;
K_ROLLBACK : R O L L B A C K;
K_ROW : R O W;
K_SAVEPOINT : S A V E P O I N T;
K_SELECT : S E L E C T;
K_SET : S E T;
K_TABLE : T A B L E;
K_TEMP : T E M P;
K_TEMPORARY : T E M P O R A R Y;
K_THEN : T H E N;
K_TO : T O;
K_TRANSACTION : T R A N S A C T I O N;
K_TRIGGER : T R I G G E R;
K_UNION : U N I O N;
K_UNIQUE : U N I Q U E;
K_UPDATE : U P D A T E;
K_USING : U S I N G;
K_VACUUM : V A C U U M;
K_VALUES : V A L U E S;
K_VIEW : V I E W;
K_VIRTUAL : V I R T U A L;
K_WHEN : W H E N;
K_WHERE : W H E R E;
K_WITH : W I T H;
K_WITHOUT : W I T H O U T;

IDENTIFIER
 : '"' (~'"' | '""')* '"'
 | '`' (~'`' | '``')* '`'
 | '[' ~']'* ']'
 | [a-zA-Z_] [a-zA-Z_0-9]* // TODO check: needs more chars in set
 ;

NUMERIC_LITERAL
 : DIGIT+ ( '.' DIGIT* )? ( E [-+]? DIGIT+ )?
 | '.' DIGIT+ ( E [-+]? DIGIT+ )?
 ;

 BIND_PARAMETER
 : '?' DIGIT*
 | [:@$] IDENTIFIER
 ;

STRING_LITERAL
 : '\'' ( ~'\'' | '\'\'' )* '\''
 ;

BLOB_LITERAL
 : X STRING_LITERAL
 ;

 SINGLE_LINE_COMMENT
 : '--' ~[\r\n]* -> channel(HIDDEN)
 ;

MULTILINE_COMMENT
 : '/*' .*? ( '*/' | EOF ) -> channel(HIDDEN)
 ;

 SPACES
 : [ \u000B\t\r\n] -> channel(HIDDEN)
 ;
```