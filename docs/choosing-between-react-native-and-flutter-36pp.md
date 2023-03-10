# 在反应原生和颤动之间选择

> 原文：<https://dev.to/andreicalazans/choosing-between-react-native-and-flutter-36pp>

[![](img/92824b847e3270d13a3f8079e39646f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vtvyjtmG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cg8t4orp07x37epr5nl2.png)

*十字贴来自[我自己的博文](https://andrei-calazans.com/posts/2019-07-30/choose-between-flutter-react-native/)T3】*

Flutter 的兴起给新移动项目的决策带来了疑问。这种怀疑早在 2016/17 年就不存在了，当时有人需要跨平台的移动解决方案。回到 2016/17 赛季，选择更少，很明显，如果你想要一个跨平台的 JavaScript 解决方案，你会选择 React Native，因为它的表现超过了竞争对手。

我们不再质疑跨平台的可行性，我们现在决定哪个解决方案是最好的。尽管有其他可能的解决方案，如 Xamarin 等，React Native 和 Flutter 都是很好的选择。

在决定哪一个是项目需求的最佳选择之前，应该知道什么？

## 体系结构

首先必须知道 React Native 和 Flutter 之间的核心区别。对于每个平台，React Native 呈现本机窗口小部件，其 JSX 组件转换为本机对应组件。这不同于以前使用 JavaScript 编写移动应用程序的尝试，因为尽管使用 JavaScript 来声明 UI 元素，但 React Native 是在呈现平台原生 UI 小部件。

另一方面，Flutter 不呈现本地窗口小部件。它依靠一个渲染引擎来绘制 2D UI 元素。Flutter 的引擎[主要用 C++](https://en.wikipedia.org/wiki/C%2B%2B) 编写，使用[谷歌的 Skia 图形库](https://en.wikipedia.org/wiki/Skia_Graphics_Engine)提供低级[渲染](https://en.wikipedia.org/wiki/Rendering_(computer_graphics))支持。这种架构决策为 Flutter 提供了对渲染的底层控制，从而使其能够提高性能。

当通过渲染引擎或平台的小部件渲染时，大多数 UI 元素不会显示太大的差异。这种差异经常在与用户交互的元素上被注意到。原生 UI 元素通常在每个平台上都有独特的行为。例如，屏幕过渡，在 iOS 上屏幕滑动，而在 Android 上滑动感觉更像是淡入。还有文本输入，这些都是平台内置的可访问性。渲染引擎必须创建文本输入、复制和粘贴、光标定位、甚至显示文本的所有基本功能。

React Native core 团队评论说，呈现原生小部件而不是呈现 UI 元素本身是一个有意识的决定，根据他们的说法，这似乎从来都不是正确的方法，原因如下:他们不想重新实现平台提供的一切；重新实施一切并跟上平台更新所需的工作量，包括支持新老多种操作系统外观。

而另一方面，Flutter 试图在所有平台上提供相同的 UI 外观和感觉，reflectly，一个最初用 React Native 编写的应用程序在 mind⁴.用这个重写了 Flutter 使 Reflectly 开发团队能够在 iOS 上编写一个应用程序，并期望在 Android 上有完全相同的外观和感觉。

编写跨平台解决方案的努力是不可忽视的，因为 Flutter 比 React Native 需要更多的资源。Flutter 试图匹配原生 UI 控件的行为，尽管他们已经发布了 1.0 版本，但这是一个正在进行的 work⁵.

## 开发者体验

什么时候开发人员的经验决定了在项目中使用的技术？希望永远如此。良好的开发经验使开发团队能够写出更好的软件。这让开发者很开心。React Native 通过引入热重载和声明式 UI framework⁶.对移动开发产生了重大影响这两点提高了开发人员的生产力和整体幸福感。

在使用 React Native 开发移动应用程序近 3 年后，第一次使用 Flutter 给我留下了非常好的印象。它通过提供 Flutter Docter、Hot reload 和文档齐全的文档，在创建出色的入职体验方面做得非常出色。可以肯定地说，任何有移动开发经验的人都可以非常快地使用 Flutter。

React 原生开发者体验是一个持续改进的过程。直到去年，对开源社区的关注以及随之而来的开发者体验，如果与今天所付出的努力相比的话，还是比较少的。自去年年底以来，React Native 的核心团队在改善与开源社区的互动方面做出了明显的努力。这些努力可以通过“你不喜欢 React Native 的什么？”版本 [1](https://github.com/react-native-community/discussions-and-proposals/issues/64) 和 [2](https://github.com/react-native-community/discussions-and-proposals/issues/134) 。丹·阿布拉莫夫目前正在负责修复热装 module⁷，它已经出现了一些问题。

## 可用的 UI 元素

Flutter 和 React Native 都提供了您需要的所有 UI 元素，尽管如此，它们都采取了不同的方法。Flutter 试图为你可能需要的所有 UI 元素提供一流的支持，它有一个很长的 widgets⁷列表，它的目标本质上是让你不需要任何第三方集成，它鼓励你编写自己的小部件。

React Native 另一方面，由于它是一个呈现原生 UI 小部件的应用程序，它正在努力实现一个精简的 core⁹，其中 React Native 将托管您需要的最小模块集，并将任何其他依赖项留给第三方开源管理。有了 React Native，就没有集成本地视图或模块的开销，只有通过桥进行通信的成本，这就是代号为 fabric ⁰.的架构重写的原因

对一些人来说，本地精益核心战略听起来是个坏主意，从主回购中移除一些 UI 元素意味着脸书以外的其他人将不得不站出来做出贡献。精益核心最终是一个积极的惊喜，所有提取的模块都得到了维护者的支持，并且比以往更新得更多。

需要注意的是，如果你的应用需要原生 UI 视图集成，Flutter 负责所有的渲染，因此，当你需要渲染原生 UI 视图时，无论是 Android 还是 iOS，它都必须将视图嵌入 Flutter 的层次结构中。对于 iOS，文档指出这是一个昂贵的操作，我将补充说，对于 Android，它不应该超级平滑，因为嵌入视图不是直截了当的。

## 升级

更新时，主要问题是中断更改。由于更新，React Native 的更长寿命显示了多次挫折。这不是 React Native 独有的，而是一个软件开发问题。尽管 Flutter 的存在时间较短，但它已经遇到了需要引入突破性变化的时刻。在 Flutter 核心团队的一项调查中，它询问用户为了更大的利益而打破常规是否可以接受。

有证据表明，两个社区都在尽最大努力通过提供清晰的文档和工具来帮助解决更新问题。Flutter 为此提供了一个 Github Wiki。而 React Native 的核心团队和社区已经创建了一个工具⁴和文档来处理这个问题。

尽管核心团队做出了努力，但他们也不能避免目标平台更新带来的问题。AndroidX 一直是努力正确适应它的原因⁵.

## 社区

对于开源项目来说，一个健康的社区可能是一个强大的资产。虽然 Flutter 是一个相当新的技术，但是它已经获得了很大的发展，并且正在成长。从 Flutter 2019 年的首次用户调查中可以看出其增长的证据。React Native 的社区相当强大，由整个 JavaScript 生态系统驱动。两者都接受外部开发团队的贡献，尤其是 React Native 接受了微软、Callstack 和⁹.博览会等公司的许多贡献颤振也有许多外部因素，但是，我们不能清楚地确定它们来自哪里。在这个范畴里，旋舞和反应原生都表现出了自信。

## 表演

已经有数百篇关于 React Native 与 Flutter 的帖子，它们都肯定 Flutter 比 React Native 性能更好，然而，它们都缺乏基准。所有这些帖子都基于 Flutter 架构背后的逻辑。理论上，颤振更快。Dart 代码(包括 SDK 和您的)提前(AOT)编译到本机、ARM 和 x86 库 <sup>20，</sup>因此，本机代码消耗速度更快。React 本机 JavaScript 线程必须每隔 5 毫秒通过批量异步调用跨越一个桥。理论上，React Native 也试图用 Fabric 解决桥接问题，这将使 JavaScript 能够通过 C++中保存的主机记录更快、更同步地进行通信。

如果我们写了两个应用程序，一个在 React Native 中，另一个在 Flutter 中，有几个屏幕，你不会真正注意到区别。只有一个包含基准的帖子出现在⁶.互联网上这是一个计时器应用程序，通过查看基准测试，你可以看到它在原生 Android、Flutter 和 React 原生之间显示出非常小的差异。然而，这篇文章有一个缺陷，它没有评估性能真正重要的关键点，即动画和大列表。

动画是表演的关键点。用户将在慢速设备上感受到延迟。默认情况下，React Native 将在这次测试中失败，它附带了一个在 JavaScript 线程上编写和运行的动画 API。有许多关于动画 API 如何缓慢⁷.的帖子要解决这个问题，您可以使用 React Native Reanimated，它通过在本机线程⁸.上运行所有内容来解决这个问题

即使 React Native 可能会改进动画，但你不能否认 Flutter 使用 Skia 作为渲染引擎的事实。这是一个非常高性能的框架，在 Flutter 的发布中，它展示了在 Android 设备上渲染 120 FPS 的能力。

对于列表，Flutter 还提供了一个专门针对大型列表的现成 API，这一点令人惊叹。ListView.builder 提供开箱即用的虚拟化，具有真正的高性能。在 React Native 上，您可以使用 FlatList 实现可接受的性能，但是，正如您在[优化 FlatList 配置文章](https://facebook.github.io/react-native/docs/next/optimizing-flatlist-configuration)中看到的，它需要一些调整。

对 Android 设备上的交互做出反应的本地时间也一直是质疑的主题。尽管如此，为了改善这一点，脸书动用资源编写了一个 JavaScript 引擎，以提高其在 Android 设备上的性能。Hermes 是一个针对 React Native 优化的 JavaScript 引擎。

## 结论

还应该考虑其他一些因素，比如生态系统。JavaScript 是最流行和最常用的语言之一。在 G2i 最近的一个项目中，决定使用 React Native 而不是 Native 开发主要是因为我们希望共享 JavaScript 中已经可用的所有服务和数据逻辑。

颤振和反应原生是跨平台开发的伟大工具。任何一种都将交付高质量的应用程序。

## 关于我

嗨！我是一名专注于 JavaScript 的软件开发人员。我喜欢谈论软件开发，如果你想聊天，你可以随时联系我。

感谢您的阅读！

## 参考

[1]大多数只考虑 React Native 或 PhoneGap/Cordova，它们彼此非常不同，尽管不是 JavaScript 解决方案，但使用 Dart(其类似 C 的语法与 JavaScript 非常相似)时，不稳定，不值得考虑。
【2】维基百科贡献者。(2019 年 6 月 30 日)。颤振(软件)。在维基百科，免费的百科全书。检索 2019 年 7 月 7 日 14:04 来自[https://en.wikipedia.org/w/index.php?title=Flutter_(软件)&oldid = 904189137](https://en.wikipedia.org/w/index.php?title=Flutter_(software)&oldid=904189137)
【3】React flux QA React 核心团队。(2019 年 1 月 24 日)。在 reactiflux.com 抄本，检索自[https://www . react flux . com/transcripts/react-native-team/# you ITV-engine-one-current-binds](https://www.reactiflux.com/transcripts/react-native-team/#youitv-engine-one-currently-binds)
【4】reflectively App 演示。(2019 年 3 月 7 日)。在 2019 年 7 月 7 日从[https://youtu.be/hdOxvNQbies?t=746](https://youtu.be/hdOxvNQbies?t=746)
【5】检索到的 19 年世界移动通信大会中，我们可以在 Flutter 的资源库中找到以下与 UI 元素行为相关的问题，如果使用原生 UI Widgets 就不会存在:[https://github.com/flutter/flutter/issues/35068](https://github.com/flutter/flutter/issues/35068)，[https://github.com/flutter/flutter/issues/35577](https://github.com/flutter/flutter/issues/35577)，[https://github.com/flutter/flutter/issues/35694](https://github.com/flutter/flutter/issues/35694)。还有更多这只是一个例子。
[6]我们可以从 React Native 之后出现的声明式移动框架/工具包的数量上看到这种影响的证据。Flutter、SwiftUI、Jetpack Compose
【7】Widget 索引[https://flutter.dev/docs/reference/widgets](https://flutter.dev/docs/reference/widgets)
【8】与断热重装模块相关的票证[https://github.com/facebook/react-native/issues/18899](https://github.com/facebook/react-native/issues/18899)
【9】与精益核心相关的票证[https://github.com/facebook/react-native/issues/23313](https://github.com/facebook/react-native/issues/23313)
【10】这是对当前桥梁架构的一个很好的概述，[https://hacker noon . com/understanding-react-native-bridge-concept-e 9526066 DDB 8](https://hackernoon.com/understanding-react-native-bridge-concept-e9526066ddb8)。发布对应的 Fabric 架构[https://github . com/react-native-community/discussions-and-proposals/issues/4](https://github.com/react-native-community/discussions-and-proposals/issues/4)。
[11]嵌入 iOS 视图是一项开销很大的操作，在可能出现颤振等效的情况下应该避免。[https://API . Flutter . dev/Flutter/widgets/uikit view-class . html](https://api.flutter.dev/flutter/widgets/UiKitView-class.html)
【12】来自 Flutter 2019 年首次用户调查的洞察。(2019 年 4 月 11 日)。中帖，检索自[https://Medium . com/Flutter/insights-from-flutters-first-user-survey-of-2019-3659 b 02303 a5](https://medium.com/flutter/insights-from-flutters-first-user-survey-of-2019-3659b02303a5)
【13】Flutter 的处理破变 Wiki[https://github . com/Flutter/Flutter/Wiki/Tree-health # handing-breaking-changes](https://github.com/flutter/flutter/wiki/Tree-hygiene#handling-breaking-changes)
【14】React Native 的 Upgreade 助手[https://github.com/react-native-community/upgrade-helper](https://github.com/react-native-community/upgrade-helper)
[https://flutter . dev/docs/development/packages-and-plugins/androidx-compatibility](https://flutter.dev/docs/development/packages-and-plugins/androidx-compatibility)
[https://github . com/react-native-community/discussions-and-proposals/issues/129](https://github.com/react-native-community/discussions-and-proposals/issues/129)
【16】Alex suli van-考察 Native、Flutter 和 React 原生移动开发之间的性能差异。Thoughtbot 开发网站:[https://thoughtbot . com/blog/examing-performance-differences-between-Native-flutter-and-React-mobile-development](https://thoughtbot.com/blog/examining-performance-differences-between-native-flutter-and-react-native-mobile-development)
【17】各种投诉动画性能的帖子:[https://www . Reddit . com/r/React Native/comments/6ex9y 1/brustly _ slow _ animations _ on _ on _ Android/](https://www.reddit.com/r/reactnative/comments/6ex9y1/brutally_slow_animations_on_android/)，[https://stack overflow . com/questions/4892828](https://stackoverflow.com/questions/48928229/slow-animations-in-reactnatives-android-app)