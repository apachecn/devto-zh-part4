# 将 Flutter 应用程序转移到生产中——移动和网络

> 原文：<https://dev.to/tomavelev/moving-flutter-app-to-production-mobile-and-web-4234>

上周末，我向公众发布了我的应用程序 What You Eat 的第一个相对稳定的版本[——这一次，是用](https://www.linkedin.com/posts/toma-velev-38180034_flutter-crossplatform-application-activity-6563881445872537600-X--O) [Flutter 框架](http://flutter.io)制作的。我的目标首先是 [iOS 平台](https://apps.apple.com/bg/app/what-you-eat/id1031848539)，因为我几年前就停止了旧 PhoneGap 版本的工作；其次是 [Android](https://lnkd.in/gNUknVf) 和[Web](https://kakvoiadesh.com/appv2/#/Search)——目前可以从 beta URL/Places 获得——你不能用一个全新的“酷”框架来完全重新实现一切。目前我没有计划将我的应用程序打包成桌面应用程序，但是如果我想的话——由于 Flutter 框架即将推出的特性，将您的代码转换成 Chromium 中使用的快速本机代码并不困难。

使用任何技术或技术堆栈的好处是编写更少的代码。当你写代码时，它没有被测试。大多数开发人员以最好的积极方式(称为快乐路径)经历逻辑的工作流。这就是为什么让别人来测试它总是一个好主意。框架的代码经过了许多人的测试，所以如果有任何错误，社区会发现它们并向公众开放，这样它们可以比任何单一的内部开发更快地得到修复。

编写更少代码的一个方法是使用[编程设计模式](https://en.wikipedia.org/wiki/Software_design_pattern)——面向对象的、函数式的或其他的。以我的经验来看，我的同事们甚至我自己都见过很多次——设计模式在没有完全理解它们如何最小化代码的情况下被使用，然后它实际上增加了它的数量，因为一个人不理解模式——它将增加 bug。只有个人经验和实现方法才能减少代码和最小化错误。Flutter 有很多选项，如果你选择了每一种可能性，都有可能变得疯狂。我在 Android 中使用过 MVVM，所以我试图在我的 Dart 代码中保持它不变。我已经更新了我的 [GeneratorApp](https://tomavelev.com/GeneratorApp/) 来生成 Kotlin Android 和 Flutter MVVM 代码。

在进入 Flutter 之前，你必须记住的第一件事是，它是一个用户界面框架。我对它在所有平台上的表现印象非常深刻——用户界面组件、动画、编码速度，到处都非常接近 1:1 的行为——当与 UI 相关的业务逻辑出现问题时，它在所有平台上都是问题，当它在一个地方得到解决时，它在其他地方也得到解决。但是对于其他任何东西来说——都有插件，当这些插件不是最新的时候，它们会在意想不到的时刻给你带来麻烦。

需要注意的一件事是，这个框架实际上迫使你考虑的是大小。小部件的面积/空间大小——它们应该被放置/涂抹到:

当你有图片，尤其是来自互联网的图片时，限制它们是一个好主意——给它们固定的大小(宽度或高度)或百分比(MediaQuery)。的*(上下文). size.width / 4)。否则它们可能会变小或变大，这取决于源文件。*

当你的文本组件来自国际化的地方、用户生成的字段，甚至是在不同于开发环境的规模上运行应用程序时，文本往往会变得混乱。使用了几种不同的技术——如果文本在行或列内——用扩展的方式将其换行——填充剩余区域，Flex/Flexible——填充一部分区域，fitted box with fit:box fit . scale down——缩小文本。

Flutter 在部件/组件方面塑造了你的思维。一般来说，在自己的组件或实用程序包装器或方法中包装重复的 UI 元素越多，需要编写的代码就越少，如果需要的话，在一个地方而不是十个地方修改就越容易。同样，在部件封装的情况下，通过标准的面向对象扩展使代码更具可扩展性。这也有不好的一面。颤振团队不是无止境的，不可能有时间、精力和知识来创造所有可能的组件。因此，许多流行的用户界面元素目前可以通过[社区插件](https://pub.dev/)获得。而且插件很多时候带来不好的开发者体验。

尽量减少代码中引用插件代码的地方，并通过基本参数(整数、字符串等)或自己的接口/模型/组件来公开。我有过这样的亲身经历。一些插件可能无法在其他移动平台上运行，尤其是网络，因为它仍处于技术预览阶段。关于网络——幸运的是，我有一些网络经验，我能够复制更流行的东西，因为你可以从 dart:html 访问 DOM API/JavaScript。

在 web 上无法访问 SharedPreference 接口，但是 Web 具有 [localStorage API。](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)将对这个键值存储的调用包装在 get/set utils 中，传递 key(-value)对，甚至是 Map < String，String >并使这个插件只在一个地方被引用是个好主意。

显示 HTML 不是 Flutter 核心的一部分，有一个用于移动平台的插件——**[Flutter _ HTML](https://pub.dev/packages/flutter_html)**。它在 web 上是不可用的，但是对于 web 来说，HTML 是“本机代码”。我用如下的 util 方法解决了这个问题:
var div = dive element()；
div . innerhtml = code；
返回 div.text

我用过 **[连接](https://pub.dev/packages/connectivity)** 插件。根据我的经验——在我的亚马逊平板电脑上听 onConnectivityChanged **不能用**。所以我把它换成了“去网络设置”和“没有互联网连接”标签下面的重试按钮。在网络上有这样的事件:
`[window.addEventListener('online',](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/Online_and_offline_events)`[fn)；](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/Online_and_offline_events) `window.addEventListener('offline',`fn)；但是，为了一致性，我没有使用它们——保持相同的行为。对于“isConnected”检查，web 上有[navigator . isoline](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/onLine)和一个 connectivity . check connectivity()API，它可以在我所有的移动设备和模拟器/仿真器上运行。

在开发我的应用程序时，我对我的 [TranslateApp](https://tomavelev.com/TranslateApp/) 做了一些改进，因此它为 Flutter 平台生成了国际化代码。在 Web 上，I18n 包目前不是 100%工作，但这并没有阻止我在浏览器中实现它，改变它的初始化代码的一小部分——注释 flutter only 的东西，改变当前语言的源代码，等等。除了我自己的代码和配置中的一些问题，它实际上工作得相当好。较小设备上的文本选择有一个问题:【https://github.com/flutter/flutter/issues/35826】T2 剪切/复制/粘贴本地组件不在视图中，并显示黄/黑 ui 错误。我已经离开了没有它的领域，我正在 GitHub 上看这个问题。

网络本地测试–当您没有启动 chrome 时，从命令行执行***open-a Google \ Chrome–args–disable-we B- security–user-data-dir = " "***(在 Mac 上)或其他操作系统上的等效程序，您将能够在您的本地环境中开发和测试 UI，而不需要后端服务器的本地副本，后端服务器也必须提供内容，因此所有沙盒和安全性都与生产中一样到位。实际上，下次我用 flutter 开发应用程序时，我可能会从网络开始，因为处理不同的屏幕大小和调整浏览器窗口一样快。此外——网络的插件更少了，这将迫使我写更好的封装代码，这些代码将在迁移到移动设备时被替换。

在 Android 上有非常简单的实现，但非常酷的过渡动画，在 Flutter 上也非常容易——在那里它们被称为[英雄动画](https://flutter.dev/docs/development/ui/animations/hero-animations)。我以前在 native 上没有遇到过的一个问题是在一个屏幕上有两个或更多的元素标记有相同的过渡 ids 特别是在平板电脑模式下。我通过在平板模式中减少“英雄”来解决它们，并为更复杂的视图替换了 pushAndReplace。

在 flutter 中，有几种方法可以创建构建——通过命令行界面、平台的命令行或使用 IDEs 用 Android Studio 和 xCode 打开项目(因为框架生成所有必要的文件),并从 UI 构建可执行文件。

从 Flutter CLI 构建 Android install-able 对我不起作用(flutter build apk/appbundle。也许是因为我包含的一些插件。我将它们全部更改为目标/编译相同的 sdk 版本，放置 AndroidX 属性和参数，匹配所有 kotlin 和 gradle 版本的工具和依赖项，它没有向我显示我可以理解的好错误。我没有尝试用 gradle 从命令行构建，而是用 Android Studio UI——它成功地构建了可安装的文件。

构建 iOS 也很棘手。Flutter CLI 目前无法配置 Apple 预置描述文件/帐户。是通过 xCode 完成的。根据我的经验，从 xCode 运行/执行应用程序的某个时刻会停止工作——一些抖动引擎初始化错误。我甚至从官方 UI 发了一个包，被拒绝了(理由很充分)。但是“flutter run ios”和“flutter build ios”成功了。但是，当前构建的输出是 runner . app——而不是 IPA——这是 app store 所需要的。所以我在网上找到了一些链接，结果被接受了，而且运行良好:
[https://github.com/flutter/flutter/issues/13065](https://github.com/flutter/flutter/issues/13065)
[https://shashikantjagtap . net/full-stack-IOs-continuous-delivery-with-xcode build-and-export options-plist/](https://shashikantjagtap.net/full-stack-ios-continuous-delivery-with-xcodebuild-and-exportoptions-plist/)
[https://github.com/flutter/flutter/issues/22306](https://github.com/flutter/flutter/issues/22306)

Flutter 的 Web CLI(Flutter Web dev)实际上从第一次开始就工作正常，没有需要搜索的重大问题。我遇到了一些问题，但是构建中的错误消息很好，并且准确地指出了我需要更改的地方。

总而言之，我对此百感交集。当针对单一平台进行快速原型开发时——进展和开发可能会很快，但是当针对所有可能的环境时，框架消费者/开发者需要更多的思考和谨慎。就像其他技术一样，有利也有弊。记住，它仍然是相对较新的，它可以在未来的弱点变得更好。我们走着瞧。