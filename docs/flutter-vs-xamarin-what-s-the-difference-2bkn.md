# Flutter vs. Xamarin -有什么区别？

> 原文：<https://dev.to/algoworks/flutter-vs-xamarin-what-s-the-difference-2bkn>

Xamarin 是一个基于 C#的开源移动平台，开发人员使用它为 macO、Android、tvOS、watchOS 和 iOS 创建无缝的跨平台移动应用程序。开发者可以跨多个平台共享代码，比如 macOS 和 Windows。Xamarin 是由一个在 2011 年开发 Mono 的工程师团队发现的，后来在 2016 年被微软收购。

另一方面，Google 推出的 Flutter 也是一个开源的、跨平台的移动应用开发框架。使用 Flutter 开发的应用程序是使用 Dart 语言编写的。它的首次发布是在 2017 年，从那以后，它在开发者中的受欢迎程度逐年增长。

尽管 Xamarin 和 Flutter 都是跨平台的移动应用程序开发框架，但它们在某些因素上存在差异，如下所述-

技术架构

Xamarin 应用程序开发公司利用 Mono 执行环境创建应用程序。在 Android 平台上，Xamarin 在 Linux 上与 Android 运行时同时运行，在 iOS 上，它与 C-runtime 和 Unix 内核一起运行。

在 Dart 框架上运行的 Flutter 使用 Skia C++引擎。它的大部分组件都是内置的，因此体积相对较大。Dart 附带了 Material Design 和 Cupertino 等框架，这些框架提供了设计移动应用程序所需的所有组件。

程序设计语言

C#是用于在 Xamarin 平台上编写跨平台应用程序的编程语言。这使得熟悉 C 语言的开发人员更容易习惯使用 Xamarin 开发应用程序。

Flutter 要求开发人员了解不太流行的编程语言 Dart。Dart 是谷歌在 2011 年推出的。了解 Javascript 面向对象概念的开发人员会发现理解 Dart 语言更容易。

应用程序大小

尽管有多平台支持和较小的二进制文件大小，Xamarin 应用程序在最终构建时会导致更大的应用程序大小。此外，在这些应用程序在应用程序商店上可用之前，存在性能和代码的优化不足。

由于二进制文件更大，Flutter 应用程序更重。这是因为 Flutter 的核心引擎占用了高达 2.7 MB+，以及应用程序代码和框架，所有这些都使得 Flutter 应用程序与其他平台上构建的应用程序相比有点重。

DEVOPS 支持

尽管 Xamarin 中的自动化过程在很大程度上依赖于 Visual Studio 的配置，但 appcenter 的 cli 界面“appcenter-cli”用于配置命令行版本。Xamarin 可以利用团队城市、应用中心或 Jenkins 等 CI/CD 工具提供的服务。

另一方面，Flutter 的 CI/CD 解决方案 Codemagic 允许开发人员使用较少的配置来设置他们的 CI/CD。这使得用 Flutter 构建应用程序成为一个无缝的过程。除了 Codemagic，其他基于云的服务也可以在 Flutter 应用程序构建过程中用于 CI/CD 目的。

API 组件和 UI 开发

Xamarin 有其开发 API 的详细文档。Xamarin 运行的多个平台，如 Android、iOS、forms 等。拥有自己的一套 UI 工具，开发者可以在 Xamarin 应用开发过程中使用这些工具。单个组件，如数据库、布局、按钮、弹出窗口等。也被 Xamarin 记录了下来。

当使用 Flutter 框架时，不再需要使用第三方库和组件，因为像导航、设备 API 访问、库、测试和有用的管理等 UI 组件都是由 Flutter 本身提供的。这为开发者提供了构建基于 Flutter 的应用所需的所有工具。

Xamarin 和 Flutter 是跨平台移动应用程序开发框架领域的两个主要竞争者。这两种框架的使用很大程度上取决于移动应用程序开发公司选择的项目类型。