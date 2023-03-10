# 了解 Dart 中的反射和注释

> 原文：<https://dev.to/graphicbeacon/understanding-reflection-and-annotations-in-dart-4kn1>

*最初发布于[creativebracket.com](https://creativebracket.com/understanding-reflection-and-annotations-in-dart/)T3】*

在这个视频中，我们将探讨反射的主题，以及如何使用它来编写更干净的软件。反射允许我们在运行时检查和修改程序的结构和行为。我们将使用包含帮助类和函数的 **dart:mirrors** 库来实现这一点。最后，我们将看一个带有**注释**的常见用例。

[https://www.youtube.com/embed/_2yjPLVEGs4](https://www.youtube.com/embed/_2yjPLVEGs4)

## 用反射的基本例子

创建一个`main.dart`文件，从导入 **dart:mirrors** 库开始。在我们实现我们的反射逻辑之前，让我们用我们希望调用的方法定义一个类:

```
import 'dart:mirrors';

main() {
  // TODO: Implement Reflection logic
}

// Our example class to reflect on
class Endpoint {
  handle() => print('Request received');
} 
```

Enter fullscreen mode Exit fullscreen mode

为了反映类型和对象，我们有三种方法可以使用:

1.  反射():反射一个类的实例
2.  **reflectClass()** :在类声明上反射
3.  **reflectType()** :反射作为参数传入的类型

在我们的例子中，我们将使用`reflect()`。让我们实例化`Endpoint`并通过反射调用句柄方法:

```
main() {
  var reflectedClass = reflect(Endpoint());

  // invoke the handle() method
  reflectedclass.invoke(Symbol'handle'), []);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们运行这个文件:

```
$ dart /path/to/main.dart 
```

Enter fullscreen mode Exit fullscreen mode

反射一个实例为我们提供了一个公开`invoke()`方法的`InstanceMirror`。我们实例的成员被描述为`Symbol`类型。上面的例子通过传递它的`Symbol` ic 名和一个位置参数数组(如果指定了的话)来调用 handle 方法。

为了传递参数，让我们修改`handle()`方法的签名:

```
handle(String a) => print('Request received $a'); 
```

Enter fullscreen mode Exit fullscreen mode

并将其传递给调用:

```
reflectedClass.invoke(Symbol('handle'), ['argument 1']);
// => Request received argument 1 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`handle()`方法的字面形式:

```
reflectedClass.invoke(#handle, ['argument 1']); 
```

Enter fullscreen mode Exit fullscreen mode

如果方法名是从操作中作为值接收的，或者是作为用户输入提供的，那么可以使用`Symbol('methodName')`方法。*理想情况下，当**不**预先知道要调用的方法时。*

观看上面的完整教程视频。

→ [**获取源代码**](https://gist.github.com/graphicbeacon/9fb6c6a951168d6a31d9b2cc442bcbe2)

## 进一步阅读

*   [**镖:镜**库文档](https://api.dart.dev/stable/2.4.0/dart-mirrors/dart-mirrors-library.html)
*   [Dart 和 MongoDB 教程#1:使用 mongo_dart 包](https://creativebracket.com/dart-and-mongodb-tutorial-1)

* * *

## 最后一件事...

我很高兴地宣布，我已经在 dev.to 上点击了**超过 7000 名关注者**。非常感谢所有不断增长的兴趣、支持和我收到的反馈！

有鉴于此，我今天将推出 [**官方商城**](////bit.ly/creativebracketmerch) 。这是一个很好的机会成为运动的一部分，并保持火焰🔥真实的这将推动高质量内容的生产，涵盖的主题将超越“hello world”应用。

我还在 YouTube 频道上点击了 800 多个订户。 [**点击订阅并响铃通知**](https://youtube.com/CreativeBracket) 如果你还没有。我计划很快做一个关于代码生成的系列文章。

最后，我在 creativebracket.com 的博客上有更多的教程。一定要去看看。

**喜欢、分享并关注**我，了解 Dart 的更多内容。*谢谢。*