# 镖中的参数

> 原文：<https://dev.to/codingpizza/parameters-in-dart-3hm0>

你好。在上一篇文章中，我们一直在谈论**变量**和**函数。如果你错过了，你可以在这里查看我们之前的帖子。**

### **T3】变量 T5】**

### **T3】功能 T5】**

## 我们以前见过他们。

在之前关于**函数**的帖子中，我们谈到了*参数。*我们看到了一个例子，在这个例子中，我们需要它们作为使 IceCreamMachine 工作的成分，我们也可以说参数是函数执行其代码所需的依赖项。

## 必需的参数

必需的参数是一个函数可以使用的最基本的参数，你指定一个类型，一个名字，你就可以开始了。

我们已经在上一篇关于 sum 函数示例的文章中看到了它们:

```
Integer sum(Integer a,Integer b) {
    return a+b;
} 
```

Enter fullscreen mode Exit fullscreen mode

整数 **a** 和整数 **b，**在函数内部使用并返回。

## 可选参数

这些参数是可选的，因为在使用该函数时可以忽略它们。为了使参数可选，我们需要把它们放在签名末尾的括号**和**中，如果你使用的是**必需的参数。**让我们看看例子。

```
void printFullName(String name, String surname, [String secondName]){
    print("My name is: $name $secondName $surname");
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中我们可以看到**可选参数**是如何放在**必需参数**之前的，如果你把**可选参数**放在第一位，编译器会抱怨。

好的，但是如果变量`$secondName`没有被传递会发生什么呢？变量将为空。我们不想刊登“约翰 null Wick”，对吗？为此，我们可以添加一个默认值，在可选参数为空时使用。

要将默认值添加到可选参数**，**中，我们需要做的就是赋值。您可以在下面的示例中更好地看到这一点:

```
void printFullName(String name, String surname, [String secondName = ""]){
    print("My name is: $name $secondName $surname");
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，值将是一个空字符串，名称将正确打印。

下面说一下如何使用前面的函数。我们实际上可以这样使用它:

```
printFullName("John","Wick");
//Or
printFullName("Scarlett","Johansson","Ingrid"); 
```

Enter fullscreen mode Exit fullscreen mode

通过用括号将参数括起来，可以使所有参数可选，如下所示:

```
void printFullName([String name, String surname, String secondName]){
    print("My name is: $name $secondName $surname");
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 命名参数

这个参数允许你在函数签名中指出你要传递给哪个参数。为此，我们需要用花括号将参数括起来。

这里有一个例子:

```
void printNamedParameters(String name,String surname,{String secondName = ""}){
    print("$name $secondName $surname")
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用名字和姓氏作为必需的参数。secondName 作为**命名的参数**，如果没有传递任何东西给它，这个值将是一个空字符串。

当我们想要使用带有可选参数的最后一个函数时，我们以如下方式使用它:

```
printNamedParameters("Samuel","Jackson",secondName: "Leroy"); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，命名参数应该包含在括号内。

如果我们需要一个只有**命名参数**的函数，我们需要做的就是用花括号将函数签名中的所有参数部分括起来。

```
void printAllNamedParameters({String name,String surname,String secondName = ""}){
    print("$name $secondName $surname")
} 
```

Enter fullscreen mode Exit fullscreen mode

对于最后一个函数，我们可以指定参数，也可以改变使用它的顺序，因为参数的顺序并不重要。

例如:

```
printAllNamedParameters(name: "Scarlett",secondName:"Ingrid",surname:"Johansson"); 
```

Enter fullscreen mode Exit fullscreen mode

是不是很神奇？，这大大提高了函数的可读性。

## 就是这样

如果你已经到了这一步，我希望你喜欢这篇文章。如果你喜欢，请与你的朋友分享，并评论你希望在这一部分看到哪个主题。

## 现在轮到你了

你可以在 IDE 中尝试这些概念，比如免费的 Intellij idea 社区，你只需要安装 Dart 插件。Visual Studio 代码或在一些在线编辑器中，如[**。**](https://dartpad.dartlang.org/)

 **## 以前的帖子

如果你对更多类似的帖子感兴趣，你可以看看我其他关于 Dart 的文章。

**[变量](https://dev.to/codingpizza/variables-in-dart-4j63)**

**[功能](https://dev.to/codingpizza/functions-in-dart-175b)**

## 了解更多

事实上，我在一本免费电子书中写了更多类似的内容，这本电子书基本上是 Dart 的基础课程，可以帮助你以后开始学习 Flutter，这是一个开发多平台应用程序的很棒的框架。如果你感兴趣，你可以通过这个链接免费得到它。**