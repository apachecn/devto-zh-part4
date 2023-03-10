# KVision v0.0.37 -带 Apache Cordova 模块

> 原文：<https://dev.to/rjaros/kvision-v0-0-37-j4l>

[KVision](https://github.com/rjaros/kvision) 是为 Kotlin/JS 打造的开源 web 框架。它允许开发人员用 Kotlin 语言构建现代 web 应用程序。

我已经发布了 [KVision 0.0.37](https://github.com/rjaros/kvision/releases/tag/0.0.37) 。经过几周的工作，一个新的模块已经实现，支持使用 [Apache Cordova](https://cordova.apache.org/) 创建移动应用。

当然，用 Kotlin 构建移动应用并不是什么新鲜事。大概大部分 Kotlin 开发者都是针对 Android 的，因为它现在是这个平台的首选语言。新的 [Jetpack Compose](https://developer.android.com/jetpack/compose) Kotlin 工具包旨在简化移动 UI 开发。实验性的 [Kotlin 原生](https://kotlinlang.org/docs/reference/native-overview.html)技术允许将 Kotlin 代码编译成原生 iOS 和 Android 应用程序。

那么，用基于 Kotlin/JS 的框架和 Apache Cordova 构建移动应用程序的原因是什么呢？将 Kotlin 应用程序编译成 JavaScript，仅仅是为了在嵌入本地应用程序的浏览器中运行这些 JS 代码，这样做有什么好处吗？

是的，有一些:

*   Cordova 应用程序是完全跨平台的——你可以在 Android 和 iOS 上运行完全相同的代码。这在 Kotlin/JVM 上是不可能的，它只允许你创建 Android 应用。

*   KVision 为您提供了一致的面向对象的 UI 库(它真的功能齐全，即使它看起来不是本机的)。与 Kotlin/Native 不同，您可以编写一次 UI 代码，kot Lin/Native 允许您共享公共应用程序逻辑，但强制您为所有平台实现专用的 UI 代码。

*   将标准 KVision web 应用移植到移动设备上非常容易。事实上，几乎没有什么要做的-你只需要在你的智能手机上打包并运行这个应用程序。

*   通过将 [Webpack 的热模块替换(HMR)](https://webpack.js.org/concepts/hot-module-replacement/) 集成到您的工具链中，您可以在开发阶段在手机模拟器中快速热重装您的应用。

如果你想找到更多的信息和用法示例，你可以阅读文档中新的[章节。](https://kvision.gitbook.io/kvision-guide/part-2-advanced-features/building-with-apache-cordova)

真的很值得一试！

KVision 0.0.37 还带来了一些其他显著的特性——看看 changelog 和所有已关闭的 GitHub 问题。

如果你喜欢，别忘了⭐️我的[项目](https://github.com/rjaros/kvision)！

感谢任何反馈！