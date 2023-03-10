# Dart 中的变量

> 原文：<https://dev.to/jay_tillu/variables-in-dart-54bk>

变量声明及其基础是任何编程语言的基本构造块。让我们稍微探讨一下 dart 变量。

> **变量是内存中存储值的命名空间。你可以说它是数据的容器。**

#### 以下是变量的命名规则。

*   标识符不能是关键字。
*   标识符可以包含字母和数字。但是标识符不能以数字开头。
*   标识符不能包含空格和除下划线(_)和美元($)符号以外的特殊字符。

## 变量声明语法

*   在 dart 中，变量必须在使用前声明。
*   创建和初始化变量的示例。

```
var name = “Akshay”; 
```

Enter fullscreen mode Exit fullscreen mode

*   这里 ***var*** 是用来声明变量的关键字。
*   Dart 支持类型推理。所以这里编译器会通过初始化器自动知道这是一个字符串类型的变量。
*   在这里，您可以通过给变量添加前缀来明确指定变量的类型。考虑下面的例子:

```
String name = "Akshay";
int highScore = 120; 
```

Enter fullscreen mode Exit fullscreen mode

dart 中的所有变量都存储对值的引用，而不是包含值。这里变量 **name** 包含一个对值为“Viveki”的字符串对象的引用。

### 动态型

*   没有静态类型声明的变量是隐式动态的。
*   如果变量不限于单类型，则将其指定为动态类型。

```
dynamic score = 56; 
```

Enter fullscreen mode Exit fullscreen mode

## 默认值

所有未初始化的变量都有初始值 ***null*** 。

即使是数字类型的变量最初也是空的，因为数字和 dart 中的其他东西一样都是对象。

```
void main() {
  int highScore;
  print(highScore);
}

Output
null 
```

Enter fullscreen mode Exit fullscreen mode

这就是你需要知道的关于 dart 中变量的基础知识。如果我错过了什么，请随时告诉我。我很乐意学习它。

直到那时，继续爱，继续编码。我将在下一篇文章中介绍您。

记住没有老师，没有书，没有视频教程，也没有博客能教会你一切。有人说，学习是一个旅程，旅程永无止境。只是从这里那里收集一些数据，读一读，学一学，练一练，试着去应用。不要因为做不到或者不知道这个概念或者那个概念而犹豫。记住，每个程序员都是从你现在走的这条路上走过的。记住每个大师都曾经是初学者。努力工作，全力以赴。

### 欲了解更多信息，请访问以下链接

*   [Fuchsia OS 官方网站](https://fuchsia.dev/)
*   [Dart 官方网站](https://dart.dev/)
*   [颤振官方网站](https://flutter.dev/)

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接在[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)给我发邮件