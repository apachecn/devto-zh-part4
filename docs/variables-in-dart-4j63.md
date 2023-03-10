# Dart 中的变量

> 原文：<https://dev.to/codingpizza/variables-in-dart-4j63>

# 变量- EN

## 什么是变量？

在这篇文章中，我们将学习 Dart 中的变量，变量就像一个小盒子，保存着对一个值的引用，这个小盒子可以有一个名字，这样我们就可以很容易地识别和记住它。

## 我们如何在 Dart 中创建它们？

在 Dart 中，我们可以通过三种方式创建变量:

### **使用保留关键字 var**

我们可以在变量名和值前使用保留关键字 var 来创建一个变量。这里有一个例子:

```
var year = 1990;
print(year)
//The result is going to be: 1990 
```

Enter fullscreen mode Exit fullscreen mode

这就是所谓的**式推理。**它的作用是编译器可以自动推导出变量或表达式的类型。

### 声明变量的类型

我们也可以如下声明变量的类型:

```
String name = "John";

print(name)

//The result is going to be: John 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
**使用保留关键字动态**

dynamic 关键字允许我们声明一个变量，该变量的类型可以在执行类型时改变，并且可以这样定义:

```
dynamic changeType = 1234

The type can change at runtime

changeType = "I'm a string now"

//The result is going to be: I'm a string now 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
现在轮到你了

你可以在 IDE 中尝试这些概念，比如免费的 Intellij idea 社区，你只需要安装 Dart 插件。或者在一些在线编辑器里像**。**

 **## 了解更多

如果你喜欢这篇文章，我实际上在一个免费的电子书中写了更多类似的内容，这是 Dart 的基础课程，可以帮助你从 Flutter 开始，这是一个开发多平台应用程序的可怕框架。如果你感兴趣，你可以点击这个链接免费得到它。。**