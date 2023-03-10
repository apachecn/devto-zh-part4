# Flutter 应用程序的容器/依赖注入

> 原文：<https://dev.to/pedromassango/a-container-dependecy-injection-for-flutter-apps-ag0>

在过去的几个月里，我们制作了一个简单的小型容器库，供我目前工作的公司内部使用。它可以在 GitHub 上的这个[库](https://github.com/nextbss/injector_io)中找到。可以试试，留下一些反馈。我们将继续保持下去，因为我们将它用于真正的项目。

### 特性

*   创建单一实例；
*   创建工厂实例(在每次调用时重新创建)；
*   使用`Module`注册实例；
*   使用`get()`函数从任何地方获取实例。
*   在`DEBUG`模式下打印日志。
*   容易测试。
*   不使用反射。

### 概念

*   **get()** = >用于解析注册类的实例。
*   **inject()** = >用于解析模块内部的依赖关系。
*   **single()** = >用于注册一个 singleton 实例。每次使用`get()`时，您都会收到一个相同的实例。
*   **factory()** = >用于注册一个工厂实例。每次使用`get()`时，您都会收到一个新的实例。

点击这里阅读更多[并开始行动。](https://pub.dev/packages/injectorio)