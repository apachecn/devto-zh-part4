# 编译器系列第 5 部分:词法分析

> 原文：<https://dev.to/miiizen/compiler-series-part-5-lexical-analysis-3kf7>

从这篇文章开始，我将解释我的编译器的各个部分以及设计它们的思路。我将链接到我的编译器储存库中的相关文件，这里是。

## 定义基本令牌

我的语言中最小的“原子”(除了个别字符)被称为记号。扫描仪的工作是识别输入文本中的有效标记，拒绝无效标记。下面的 BNF 描述了一些基本的令牌。

```
<digit> : [0-9]+
<number> ::= [<digit>]+.[<digit>]+

<name> ::= [a-zA-Z][a-zA-Z0-9]*
<string_lit> ::= " [\w*] "

<op> ::= ['+' | '-' | '*' | '/' | '^' | '%' | '=' | '>' | '<' | '!' | '|' | '&']+
<whitespace> ::= [' ' | '\n' | '\t']+
<comment> ::= # <all> 
```

字符串将组成标识符和特定的关键字以及字符串文字。运算符可以是上述符号的任意组合。扫描仪将继续识别特定的组合。如果输入字符的组合未被识别，扫描仪将抛出错误。扫描仪还会删除空白和注释。

词位是与标记匹配的任何字符组合。我的完整令牌列表如下:

```
Token Types {
    PLUS, MINUS, STAR, SLASH, HAT, MOD, INC, DEC,
    EQ, LESS, GREATER, LEQ, GREQ, NEQ,
    AND, OR, NOT,
    ASSIGN, CONDITIONAL, COLON,

    LEFTPAREN, RIGHTPAREN, LEFTSQ, RIGHTSQ, COMMA,
    NUMBER, STRING, IDENTIFIER, BOOL,
    BEGIN, IF, ENDIF, ELSE, THEN,
    FOR, IN, ENDFOR,
    DEFINE, ENDDEF, EXT,
    NEWLINE, END
} 
```

## 有些语言理论 <sup id="fnref1">[1](#fn1)</sup>

扫描仪识别乔姆斯基等级体系 <sup id="fnref2">[2](#fn2)</sup> 中的类型 3 语言。类型 3 语言可以被正则表达式和状态机识别/解析。我可以为解析器实现一个字面上的状态机，但是我不会这样做，因为这种方法写起来效率不是很高。类似地，我本可以编写一个复杂的正则表达式来将输入拆分成标记，但是这对我来说混淆了很多逻辑。我开始这个项目是为了学习，而不是用尽可能少的行写一个编译器。
稍后，解析器将使用下推自动机解释第二类语言。(一种自动机，使用堆栈来保存信息。在我的实现中，这个栈是 C++函数调用栈)。被解释的两种语言之间的差异是拥有单独的词法分析器和解析器 <sup id="fnref3">[3](#fn3)</sup> 的主要原因。
[![The Chomsky Hierarchy](img/d00caab2e8e38f1a6cd6a08676165146.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VDM8nhHk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e5v1ojd7td4t5w27ny9w.png) 
存在一个名为 flex <sup id="fnref4">[4](#fn4)</sup> 的工具，可以在 c 中生成扫描器状态机，这是 GNU lex 的现代版。我认为这个工具给我的代码带来了太多的开销。

## 算法

扫描仪的基本算法如下。`getString()`和`getOp()`等函数在上面的 BNF 中有描述。

```
getNextTok =>
   nextChar()
   if next char is '#' skip line then nextChar() 
   skip whitespace

   if next char is digit, getNumber() and return number token

   getName() if next char is alpha
       if string is keyword return keyword token
       else return identifier token

   getOp() if next char is operator
       if operator string is recognized return operator token
       else error

   getString() if next char is '"'

   if next char is recognized punctuation
       return punctuation token

   if next char is unrecognized
        error 
```

当您需要解析任何类型的输入时，编写一个扫描器一点也不困难，并且应该对许多项目有用。
最后，[这里的](https://github.com/miiizen/Compiler/blob/master/Compiler_Lib/scanner.cpp)是我编译器里的扫描器。

* * *

1.  Aho，a .，Lam，m .，Sethi，r .和 Ullman，J. (2006 年)。编译器:原理、技术和工具。第二版。培生教育公司 [↩](#fnref1)

2.  [计算理论——乔姆斯基等级体系](https://www.geeksforgeeks.org/toc-chomsky-hierarchy/)↩

3.  让我们构建一个编译器，作者杰克·克伦肖——第七部分:词法扫描 [↩](#fnref3)

4.  [GitHub - westes/flex:快速词法分析器](https://github.com/westes/flex) [↩](#fnref4)