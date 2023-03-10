# Dart 中的函数

> 原文：<https://dev.to/codingpizza/functions-in-dart-175b>

# 功能- EN

在本节中，我们将讨论函数、它如何在 Dart 上工作以及函数类型。

## 🤔什么是函数？

函数是一个代码块，它应该被组织起来，执行一个单一的任务，并且应该与我们正在处理的类相关。

功能也应该是可重用的。这将减少代码的数量，提高代码的质量。如果做对了。

## 👨‍🔬功能解剖学

Dart 中有许多函数类型。首先，我们将学习一个函数是如何工作的，然后我们将解释其他函数类型。

这些函数通常使用以下语法创建:

```
void printName(String name) {
  print(name)
} 
```

Enter fullscreen mode Exit fullscreen mode

**其中**:

**void** 是函数的返回类型。这意味着当函数执行内部所有代码时，它应该返回这个值。

等一下。我们不归还任何东西，我们只是打印一个名字。这是因为当我们不需要返回值时，我们使用保留关键字 void。

**printName** 是函数的名字，这个名字我们以后会用到，这个名字应该解释这个函数是什么。

**(字符串名称)，**在这个括号内，我们要指定参数的类型和参数的名称。参数是函数内部可用的变量，我们将在下一章更多地讨论参数。

我们来看一个经典的例子:

```
Integer sum(Integer a,Integer b) {
    return a+b;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数帮助我们对两个整数求和，我们提供第一个值和第二个值，它返回两个值的和。我们可以如下使用它:

```
String result = sum(2+2);
print(result);

//Result from the execution: 4 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们创建了一个名为 result 的变量，它存储我们从函数 **sum，**中得到的结果

当我们打印时，结果是 4。

简而言之，我们可以说一个函数的工作原理就像一个冰淇淋机，你添加配料(参数)，它应该会返回给你一个美味的冰淇淋。让我们看另一个例子:

```
AwesomeIceCream IceCreamMachine(String flavourExtract,Double coupsOfMilk,Integer sugarCup){
    return AwesomeIceCream(flavourExtract,coupsOfMilk,sugarCup)
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个更高级的例子中，我们创建了一个名为 IceCreamMachine 的函数，它返回一个 AwesomeIceCream 对象。对象是一个类的实例。我们待会再讨论。但是请记住，AwesomeIceCream 是一个类似字符串、整数或双精度的对象。

## ➡️Arrow 函数

arrow 函数是一个只有一行代码的函数，你可能会注意到它没有大括号，而是有一个箭头。

```
void printName(String name) => print(name); 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的函数有助于我们保持函数较小，并提高代码可读性。让我们把其他例子转换成一个箭头函数。

让我们从 *sum 函数开始。在*第一个地方，我们需要去掉括号，加上箭头；在箭头符号之后，我们添加函数的逻辑。就是这样！您成功地创建了您的第一个箭头函数。

```
Integer sum(Integer a,Integer b) => a+b; 
```

Enter fullscreen mode Exit fullscreen mode

现在为什么不试着转换一下*冰淇淋机*的功能？。

## 匿名函数

正如我们之前所说的，函数应该有一个名字，但是在匿名函数的情况下，它没有，这个函数被称为 in-site a 作为参数传递给其他函数🤯。

```
var icecreamFlavours = ['chocolate', 'vanilla', 'orange'];
icecreamFlavours.forEach((item) {
  print('We have the $item flavour');
}); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:foreach function 所做的是为列表中的每个元素执行其内部的代码。我们将在另一章看到更多关于这些函数和集合的内容。

这将产生以下输出:

```
We have the chocolate flavour
We have the vanilla flavour
We have the orange flavour 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么？。forEach() **函数**作为参数接收一个函数，在 Dart **函数**中，函数是一个和其他函数一样的类型。

对于匿名函数，语法如下:

```
(parameterName) {
  Body of the function
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你仍然感到困惑，你可以把它看作下面这个例子的简写:
假设你有一个列表，你想迭代它来打印条目，但是你不想用你现在的方法来打印条目。类似这样的。

```
var list = List.from([1, 2, 3, 4, 5, 6]);
list.forEach(
//Here we should print our items
); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们希望在另一个方法中拥有这种逻辑，所以我们可以创建一个名为`getPrintElementFunction()`的函数。

```
void function; getPrintElementFunction() {
  return (item) {
    print("The number is $item");
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数返回一个函数🤯。这个功能相当少见。您可以在 forEach 方法中使用函数，如下所示:

```
list.forEach(getPrintElementFunction()); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码可以编译并打印出和我们之前看到的函数一样的结果，但是如果你想在里面做一些简单的事情，那就太难看了。

## 现在轮到你了

你可以在 IDE 中尝试这些概念，比如免费的 Intellij idea 社区，你只需要安装 Dart 插件。Visual Studio 代码或者在一些在线编辑器中比如 [**Dartpad。**T3】](https://dartpad.dartlang.org/)

## 以前的帖子

如果你对更多类似的帖子感兴趣，你可以看看我其他关于 Dart 的文章。

[**变量。**T3】](https://dev.to/codingpizza/functions-in-dart-175b/)

## 了解更多

如果你喜欢这篇文章，我实际上在一个免费的电子书中写了更多类似的内容，这基本上是 Dart 的基础课程，可以帮助你以后开始学习 Flutter，这是一个开发多平台应用程序的可怕框架。如果你感兴趣，你可以点击这个链接免费获得。