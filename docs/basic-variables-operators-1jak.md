# 基本变量和运算符

> 原文：<https://dev.to/giladri/basic-variables-operators-1jak>

就像在代数中一样，在编程中我们也有变量。变量是“盒子”，有一个名字，包含一个值。**名字**可以是一个字母，比如‘x’，一个单词比如‘grade’，甚至几个单词比如‘max grade’(C 中的约定是第一个单词总是以小写字母开头，其他每个单词都以大写字母开头，中间没有空格和标点符号。这个惯例叫做[骆驼案](https://en.wikipedia.org/wiki/Camel_case)。您可以在名称中添加一个数字，但它必须位于至少一个字母之后，例如:“x1”(而不是~~“1x”~~)。**值**可以是数字 10(如代数中:x=10)、字符“h”、单词“hello”甚至是一辆汽车！

在 C 语言中，每个盒子都有一个类型。只有这种类型的值可以进入盒子——就像婴儿玩具一样，它的盒子上有不同形状的洞。如果一个婴儿试图(当然，他们总是试图)把一个形状放在错误的地方——它不会进入盒子。在我们的例子中，“计算机”会告诉我们有一个问题:变量和我们试图放入的值的类型不匹配。

好的，到目前为止还不错。但是我们如何创建一个变量，如何把一些东西放进去呢？

第一个问题的答案是:

```
[variable_type] [variable_name]; 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
int x; 
```

Enter fullscreen mode Exit fullscreen mode

#### **通知！在每一行的末尾，我们必须加上分号('；'))**

什么是“int”？这是一种变量。变量的基本类型有:

# **基本变量类型**:

*   **char** :“人物”一词的快捷方式。在这个变量类型中，我们保存了**一个**字母或者**一个**数字。此变量的值必须介于“”之间。例如，字母“a”。**注意，在 C 中,‘和”**是有区别的。(双引号用于字符串，字符串是一组字符。我们以后再说)。
*   **int** :单词“整数”的快捷方式。在这个变量类型中，我们保存一个整数——一个没有点的数(可以是负数)。比如 10。
*   **float** :“浮点运算”的捷径。在这个变量类型中，我们保存一个实数，当然包括整数。比如 10.25。
*   **double** :类似 float，但是可以包含更大的数字。我们以后再谈。(目前，您可以同时使用 float 和 double，并假设它们是相同的)。

好了，现在，来回答第二个问题(还记得吗？).为了在变量中插入一个值，我们必须使用' = '操作符，意思是“插入”。比如:

```
int x = 10; 
```

Enter fullscreen mode Exit fullscreen mode

就像在代数里一样！我们“告诉计算机”,我们命名为“x”的盒子包含了整数值 10。

# **评论**:

在我们继续讨论更多的数学运算符之前，我想教你关于**注释**的知识。注释是代码中非常重要的行，程序员用**英语**写下他/她的代码。有时候，评论是这样的:“当我写这段代码的时候，只有我和上帝知道它做了什么——现在只有上帝知道”——但通常它们真的很有用，比如:

```
// The variable "maxGrade" contains the max grade you can get in the test
int maxGrade = 100; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，注释的格式是:

```
// Some text before a line
int x = 1;

int y = 2; // Some text at the end of a line

// This doesn't work, because the code is also in the comment: int z = 3; 
```

Enter fullscreen mode Exit fullscreen mode

如果你想写一个超过一行的长注释，你可以使用:

```
/* 
Some text
Some more text
And... that's it!
*/ 
```

Enter fullscreen mode Exit fullscreen mode

# **基础运算符**:

好的。这就是你需要知道的关于评论的全部内容，至少现在是这样。让我们回头来谈谈数学运算符。和代数一样，我们有基本的数学运算:加、减、乘、除。
在 C 中，对于这些运算，我们可以使用下面的数学运算符:

```
int x = 10;
// The plus operator, for addition
int a = x + 1; // a is 11
// The minus operator, for subtraction
int b = x - 1; // b is 9
// The asterisk operator, for multiplication
int z = x * 2; // z is 20
// The slash operator, for division
int w = x / 2; // w is 5 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想改变一个变量，并把改变保存在同一个地方，我们可以这样做:

```
int x = 1;
x = x + 1; // x is 2
x = x - 1; // x is 1
x = x * 2; // x is 2
x = x / 2; // x is 1 
```

Enter fullscreen mode Exit fullscreen mode

或者更简单地说:

```
int x = 1;
x += 1; // x is 2
x -= 1; // x is 1
x *= 2; // x is 2
x /= 2; // x is 1 
```

Enter fullscreen mode Exit fullscreen mode

这个帖子的最后两个操作符是“++”操作符和“-”操作符。这些操作符可以出现在变量之前或之后，它们只是将变量的值增加 1(用“++”)或减少 1(用“-”)。例如:

```
int x = 1;
x++; // x is 2
x--; // x is 1
++x; // x is 2
--x; // x is 1 
```

Enter fullscreen mode Exit fullscreen mode

你能猜出如果操作符在变量前面和如果操作符在变量后面有什么区别吗？老实说，这种区别并不重要，我们以后会谈到。为了让你的代码**更具可读性**，你应该把它**放在变量**之后，除非你有一个非常好的理由把它放在其他地方。

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德