# 科特林中的变量

> 原文：<https://dev.to/jay_tillu/variables-in-kotlin-13pp>

*   变量是你给计算机内存位置起的名字，用来存储计算机程序中的值。

*   它们提供了一种用描述性名称标记数据的方法，因此我们的程序可以被读者和我们自己更清楚地理解。

*   将变量想象成一个存储特定值的盒子是很有帮助的。它们的唯一目的是在内存中标记和存储数据。以便在整个程序中可以方便地使用数据。

*   kotlin 中有两种类型的变量:

*   可变变量(使用`var`关键字声明)

*   不可变变量(使用`val`关键字声明)

## 变量声明

* * *

*   要在 kotlin 中声明一个变量，要么使用`var`要么使用`val`关键字。

*   在 kotlin 中，变量声明只有两种可能。

1.  通过类型推断(内联初始化是强制性的)
2.  通过显式数据类型声明(您可以稍后在这里初始化变量)

## kot Lin 中声明变量的语法

* * *

***通过类型推理***
进行声明的语法

```
var VariableName = value    //Initializer is compulsory
val VariableName = value    //Initializer is compulsory 
```

Enter fullscreen mode Exit fullscreen mode

***语法为显式数据类型声明***

```
var VariableName  : data type = value    //Initializer is optional
val VariableName : data type            //Initializer is optional
VariableName = value              // initialize value at this point 
```

Enter fullscreen mode Exit fullscreen mode

***例如***

```
val language = “French”
var score = 40 
```

Enter fullscreen mode Exit fullscreen mode

*   这里，语言是类型为 ***字符串*** 的变量，分数是类型为 ***Int*** 的变量。

*   在 kotlin 中，您不必指定变量的数据类型。

*   编译器通过类似这里的初始化式表达式知道这一点，它知道“French”是一个 ***字符串*** ，40 是上面程序中的一个 ***整数*** 值。这在编程中称为类型推断。

*   但是，如果需要，您可以显式指定数据类型。比如说。

```
var language: String = “French”
val score: Int = 40 
```

Enter fullscreen mode Exit fullscreen mode

*   正如你所注意到的，在 kotlin 中，我们首先写变量名，然后加上一个冒号，然后指定该变量的数据类型。

*   在上面的例子中，我们已经在声明过程中初始化了变量。

*   但是，这不是必须的，您可以在一条语句中声明变量并指定其类型，然后在程序的另一条语句中初始化变量。

```
var language : String 
language = “Marathi”

val score         //Compile time Error
Score = 60        //Compile time Error 
```

Enter fullscreen mode Exit fullscreen mode

*   在最后一条语句中，没有显式指定语言变量的类型，也没有在声明期间初始化变量，这可能会导致编译时错误。你必须选择其中之一，要么在声明时初始化变量，要么显式指定其数据类型。

伙计们，这就是科特林的变量。这是关于变量的非常深入的细节。今天就到这里，伙计们。请每天查看更多文章。

在那之前，继续编码，继续爱。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)