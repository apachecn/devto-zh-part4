# 快速变量

> 原文：<https://dev.to/codingpizza/variables-en-dart-3963>

## [T1】quéson las 变量？](#qu%C3%A9-son-las-variables)

在本文中，我们将了解 Dart 中的变量，这些变量就像带有值引用的小盒子。而这些小盒子可以有一个名称，以便于识别。

## 我们能否在 Dart 中创建变量？

在 dart 中，我们可以通过三种方式创建变量:

### 使用保留字 *var*

我们可以使用保留字 *var* 在变量名之前创建一个变量，然后在变量名之后我们要赋予它的值，如下例所示:

```
var año = 1990;
print(año)
//El resultado es: 1990 
```

Enter fullscreen mode Exit fullscreen mode

**注:`print(año)`指令打印变量值。**

我们刚才看到的是一种类型推断。类型推断是 Dart 编译器的一项功能，因此您可以从正在创建的变量中推断类型。

### 声明指定类型的变量

我们还可以声明变量的类型，如下例所示:

```
String nombre = "José";

print(nombre)

//El resultado es: José 
```

Enter fullscreen mode Exit fullscreen mode

###   
**使用保留字*【动态】T7***

保留字 *dynamic* 允许我们声明一个变量，其中类型可以在运行时更改，并且可以定义如下:

```
dynamic cambioDeTipo = 1234

print(cambioDeTipo) // El resultado de esta ejecución será: 1234

cambioDeTipo = "Uno dos tres cuatro"

print(cambioDeTipo) // El resultado de esta ejecución será:"Uno dos tres cuatro" 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
现在轮到你

您可以在开发环境(IDE)中测试这些概念，例如[【intellij idea communit】](https://www.jetbrains.com/idea/download/)，而且它是免费的并安装 Dart 插件，如果您喜欢 Visual Studio 代码，您也可以在其中测试这些概念，最后，如果您喜欢在线上的东西，可以使用[**darpad**](https://dartpad.dartlang.org/)

## 知道更多 Dart 剩馀？

如果您喜欢这篇文章，并且对学习 Dart 感兴趣，我现在正在编写更多这样的文章，在 ebook 中，这是 Dart 的基础课程，它将帮助您掌握良好的知识，然后您可以使用它开始浮起；页:1。你可以在[这个环节上脱颖而出。](https://www.codingpizza.com/curso-dart-gratis/)。因为电子书将是完全免费的**。**