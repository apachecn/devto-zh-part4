# 颤振依赖性注射

> 原文：<https://dev.to/sduduzog/flutter-dependency-injection-1aj6>

*封面图片[来源](https://medium.com/apparence/use-dependency-injection-with-flutter-9c3ee7bf4d16)*

最近我迷上了网络和移动之间的代码共享。很明显，有许多方法可以编写在平台间共享的代码，短语“代码共享”可以包含相当多的内容。最让我感兴趣的是 Flutter、AngularDart(我将继续称之为 ngDart)和 BLoC 模式。

为了证明我的发现，我采用了 flutter 预构建的计数器应用程序，并在 ngDart 中实现了它。我确实试图让它尽可能的一样，但是我很懒，我不太注重美观。[点击此处查看回购](https://github.com/sduduzog/counter_example)

要运行 flutter 应用程序，打开 flutter 项目文件夹，或者使用 IDE 快捷方式，或者在终端中，执行
`> flutter run`
来运行 angular 应用程序，这几乎是相同的事情，但命令是
`> webdev serve`

您将看到，使用 ngDart，依赖注入框架不成问题，因为它是内置的，但使用 Flutter，我不得不四处寻找看起来合适的解决方案。

### 方案 1

[flutter _ simple _ dependency _ injection](https://pub.dev/packages/flutter_simple_dependency_injection)
我从[那篇文章](https://www.toptal.com/cross-platform/code-sharing-angular-dart-flutter-bloc)中找到了这个包，我用它来开始我的代码共享示例。如果我不认为那篇文章虽然信息量很大，但当它涉及到例子时就相当复杂，我就不会做这个项目。

### 方案二

这似乎是解决之道。他们的自述提到了迷人的花哨词语，如

> Dart 和 Flutter 的编译时依赖注入，类似 Dagger。

我被迷住了，特别是因为我刚刚把我的开源 android 应用程序移植到 Dagger 上。
我立即移植了我的例子来使用它，同时我也无法摆脱使用这个包带来的不好的感觉。

*   它不在 dart 包注册表 [pub.dev](https://pub.dev) 上，所以你必须用一种时髦的方法把它添加到你的项目中。
*   他们的自述文件中有一条注释，说明这不是谷歌或 Dart 团队的官方项目。回购实际上是由 github 上的 Google org 托管的。
*   问题跟踪器被禁用。这足以让我寻找另一种解决方案。我甚至没有看到它的实际运行。我在配置过程中停止了。

### 方案三

[dependencies _ flutter](https://pub.dev/packages/dependencies_flutter)
当移植到这个的时候，我立刻感到很舒服。它是模块化的，感觉像是 flutter Widget 系统的原生产品。作者的例子也很容易理解，我不必为了理解如何使用它而去谷歌一篇中型文章。

我希望我不要因为解决方案 3 把我赶走而去解决方案 4，而是因为我的项目将从迁移中获得什么好处。