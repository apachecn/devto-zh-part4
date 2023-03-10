# 随处可见的表情

> 原文：<https://dev.to/samizan/expressions-everywhere-46e3>

### 什么是表情？

基本上，它是解析为一个值的任何有效的代码单元。

下面是一些包含“表达式”一词的 JavaScript 术语列表。

**文字值表达式**:一个语句或另一个表达式的任何部分，完全按照原样使用，就像语句 a = b + 2 中的数字 2。

**变量表达式**:表示某个当前值的语句或其他表达式中的任意符号。一个例子是语句 a = b + 2 中的变量 b。

**算术表达式**:是数字、运算符、变量和括号的正确组合。然后它会解析为某个值。例如 a = b + 2 语句中的 b + 2。

**字符串表达式**:与算术表达式基本相同，但计算结果为字符串类型并使用字符串运算符。例如:
var mystring = ' alpha '；
mystring+= ' beta '；

**逻辑表达式**:计算结果为真或假的表达式。例如:
false || (3 == 4)

**赋值表达式**:将文字、变量或算术表达式赋给某个变量。例子是 a = b + 2。

**主表达式**:是 JavaScript 中的基本关键字和通用表达式。例如，new、super、Spread 运算符...

**匿名函数表达式**:是将一个没有名字的函数赋给另一个变量。匿名函数表达式与函数语句非常相似，语法几乎相同。例子:
let foo = function() {...};

**命名函数表达式**:将一个有名字的函数赋给另一个变量。例:
设 foo = function add(a，b){ return a+b }；

**立即调用的函数表达式(life)**:是一种立即调用的函数表达式，函数内部调用的变量不会影响 life 外部的周围代码。IFFE 可以是匿名的，也可以是命名的。例如:
函数 my life(){
...
})()；

注意:最后一个括号是调用函数表达式所必需的，而第一组括号防止函数被当作普通的函数声明。

**正则表达式**:是用来匹配字符串中字符组合的模式。它们也是对象。