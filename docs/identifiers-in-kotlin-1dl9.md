# Kotlin 中的标识符

> 原文：<https://dev.to/jay_tillu/identifiers-in-kotlin-1dl9>

*   标识符是给予对象、变量、类和函数等的名称。例如:var 年龄= 45

*   这里`var`是一个关键字，年龄是变量的名称。

## 命名标识符的规则

* * *

有五条规则适用于命名标识符。

*   **标识符可以包含字母、下划线和数字。但不能以数字开头。**

```
var 1userBalance = 2500  // Identifier cannot start with numbers.
var $dollar = 60  // Identifiers cannot contain special symbols. 
```

Enter fullscreen mode Exit fullscreen mode

*   不允许有空格。

```
var High Score  // Identifiers cannot contain whitespaces. 
```

Enter fullscreen mode Exit fullscreen mode

*   除下划线外，不允许使用特殊符号。

```
($, #, ?, <>, ~, ! Not allowed) 
```

Enter fullscreen mode Exit fullscreen mode

*   标识符区分大小写。

```
var HighScore or highScore // both highscores are different here. 
```

Enter fullscreen mode Exit fullscreen mode

*   在 kotlin 中，硬关键字不能用作标识符。

```
var class  // hard keywords cannot be used as identifiers. 
```

Enter fullscreen mode Exit fullscreen mode

*   当创建变量、类、对象或函数时，选择一个有意义的名称。例如，分数、数字、级别、用户数据比变量名(如 a、b、c)更有意义，尽管它们是有效的。

### 骆驼肠衣

*   camel case 是一种命名模式，主要用于命名编程。

*   骆驼肠衣有两种类型:

*   上驼色表壳

*   下驼色表壳

*   在编程中总是推荐使用 camel 大小写命名方法。

*   **Upper camel case —** 在 Upper camel case 中，新单词的第一个字母是大写字母，这使得它很容易与小写字母区分开来。

```
var HighScore = 250   //Example of upper camel case. 
```

Enter fullscreen mode Exit fullscreen mode

*   **小写字母—** 小写字母中，名字的第一个字母是小写字母，然后每个新单词之后都是大写字母，这样就很容易与大写字母区分开来。

```
var highScore = 250   //Example of lower camel case. 
```

Enter fullscreen mode Exit fullscreen mode

### 什么是空格？

* * *

*   在计算机程序设计中，空白是在排版中代表水平或垂直空间的任何字符或一系列字符。

```
var high Score = 250   //Compile time error. Whitespace not allowed. 
```

Enter fullscreen mode Exit fullscreen mode

*   空格键、tab 和 enter 按钮主要用于插入空格。

*   记住大多数编译器不允许空白。所以在你的程序中尽可能避免它们。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)