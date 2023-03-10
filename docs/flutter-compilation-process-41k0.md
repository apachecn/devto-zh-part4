# 颤振编译过程

> 原文：<https://dev.to/jay_tillu/flutter-compilation-process-41k0>

在学习任何框架或编程语言时，理解平台如何编译你的代码是非常必要的。整个编译过程是如何完成的。它让您更加了解平台和核心编程概念。我们学习 flutter 旅程的下一步是理解 flutter 的编译过程，以及 Flutter 如何创造它的单一代码基础魔法。所以让我们开始吧。

Flutter 中的任何 app 都是借助 Dart、C 和 C++三种语言创建的。在这里，你也可以看出每一个 flutter 应用程序都是这三种语言代码的组合。

#### 这三种语言用于什么目的？

这里，整个 Flutter 框架是在 Dart 中创建的，我们也用它来开发一个应用程序。这里还要注意，我们并不直接使用 C 和 C++代码来编写应用程序，相反，C 和 C++代码是用于图形渲染引擎和 Dart 虚拟机来执行它们的任务。

那么现在我们来了解一下 iOS 和 Android 是如何编译这三段代码的。

## 如何为 Android 编译 flutter 代码

* * *

*   图形引擎的 C 和 C++代码是在 Android 的 NDK(原生开发工具包)的帮助下编译的。

*   SDK 和我们的 dart 代码都是通过 AOT(提前)编译过程编译成本机 ARM 和 x86 库的。

*   编译成功后，生成 APK 文件。

*   任何小部件渲染、输入输出、事件处理等等都是由编译后的应用程序代码完成的。

*   这里请注意，调试模式构建使用 Dart 虚拟机来运行 Dart 代码，以便启用有状态热重装。

## 如何为 iOS 编译颤振代码

* * *

*   图形引擎的 C 和 C++代码是在 LLVM(低级虚拟机)的帮助下编译的。

*   SDK 和我们的 dart 代码都是通过 AOT(提前)编译过程编译成本机 ARM 库的。

*   编译成功后，会生成 IPA (iOS 扩展名与 APK 相同)文件。

*   任何小部件渲染、输入输出、事件处理等等都是由编译后的应用程序代码完成的。

*   这里请注意，调试模式构建使用 Dart 虚拟机来运行 Dart 代码，以便启用有状态热重装。

这就是 flutter 在两个平台上的编译过程。我认为理解它是非常容易和直接的。如果你有任何问题，请随时问我。如果我错过了什么，请告诉我，我很乐意向你学习。

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。T3】😊
> [推特](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接发邮件给我[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)