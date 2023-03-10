# 吉隆坡 2019 年国际航展上的颤动—后续

> 原文：<https://dev.to/truongsinhtn/flutter-at-io-extended-kuala-lumpur-2019-follow-up-38fg>

我刚从 IO Extended Kuala Lumpur 2019([# iox KL 19](https://twitter.com/hashtag/ioxkl19?src=hash))回来，我的演讲似乎产生了很多兴趣和问题(在 [#IOxKL19](https://twitter.com/hashtag/ioxkl19?src=hash) 的问题是使用 [https://app.sli.do](https://app.sli.do/) 异步提问的)我无法在台上回答所有问题，所以下面是后续。没有所谓的“愚蠢的问题”，所以我会回答他们。

[![](img/05c16eee3ea2d6328e607f3d2dc37124.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jP43bwnz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AeFhf9jLd9nrB1l_j7jb82Q.gif)

这些问题是按时间顺序排列的，其中一些可能已经在后面的幻灯片中回答过了，但为了完整起见，我将把所有内容都保留在这里，包括我已经给出的答案。如果您仅对某些特定主题感兴趣，请使用下面的目录进行浏览。粗体问题是我最喜欢的。

*   [为什么旋舞不做 web/hybrid app？](https://medium.com/p/9f5904a3ab26#765f)
*   对于全新开始的人有什么建议吗？T3】
*   [使用颤振的缺点是什么？](https://medium.com/p/9f5904a3ab26#ed3c)
*   [**大型银行企业的 Flutter 准备得怎么样了？**T3】](https://medium.com/p/9f5904a3ab26#9086)
*   我们能在 Flutter 中集成本地框架(iOS)吗？
*   [我们能以多快的速度在 Flutter 中构建一个自定义框架，而不是等待社区的到来？](https://medium.com/p/9f5904a3ab26#da64)
*   [可以用 Flutter 开发游戏吗？有任何故障或性能问题吗？](https://medium.com/p/9f5904a3ab26#3d48)
*   当前在 AngualarJS 上运行的应用程序，计划现在到达。考虑到颤振。有什么推荐？
*   自定义视图怎么样？性能和可行性？
*   [为什么 Sliver 和 TabBarView 不能很好地耦合，两者都有可滚动的内容🙁](https://medium.com/p/9f5904a3ab26#0bae)
*   未来 flutter_web 会并入 flutter 吗？所以在 1 个代码库中可以部署到 3 个平台(Android、iOS、Web)
*   有没有更容易获得 flutter widgets 的地方？
*   颤振的简单状态管理？
*   [**如果我已经有 Angular 的经验，选择 ionic 作为跨平台框架不是比 Flutter 更好吗？**T3】](https://medium.com/p/9f5904a3ab26#95ff)
*   我能在 Github 或其他地方得到 Flappy Bird 克隆的代码吗？😆
*   你用什么来流化你的手机屏幕？看起来很流畅
*   [我应该完全避免](https://medium.com/p/9f5904a3ab26/edit#0181) [setState 吗？](https://medium.com/p/9f5904a3ab26/edit#0181)
*   如果我已经有了原生应用，我的公司应该如何采用 Flutter？
*   您有使用 admob 和报警管理器的经验吗？当我们同时使用两个插件时，应用程序会崩溃，并产生无用的转储堆栈！

### 为什么旋舞不做 web/hybrid app？

见[如果我已经有 Angular 的经验，选择 ionic 作为跨平台框架不是比 Flutter 更好吗？](https://medium.com/p/9f5904a3ab26/edit#95ff)

### 对于全新开始的人有什么建议吗？

对于 noobs 来说，先学习基本的编程概念，比如赋值变量、比较、逻辑(and、or、xor、not)和算术运算、流量控制。然后学习使用源代码版本控制，我的偏好和推荐是 Git。然后学习单元测试，把你自己训练成一个软件工匠，并且你的心态总是遵循[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)；可选地学习和练习 TDD 和结对编程。现在你有了一个好的软件开发背景，试试这个

*   [https://flutter.dev/docs/get-started/install](https://flutter.dev/docs/get-started/install)
*   [https://codelabs.developers.google.com/?cat=Flutter](https://codelabs.developers.google.com/?cat=Flutter)

然后前往[https://github.com/flutter/flutter](https://github.com/flutter/flutter)，在那里他们有> 50 个被标记为[容易解决](https://github.com/flutter/flutter/labels/easy%20fix)的问题，解决一个并让你的公关合并。祝贺🎉，你不仅“入门”了颤振，还成为了颤振贡献者！

然后关注一些社交媒体，就像演讲后面提到的:

*   官方 Flutter [Youtube](https://www.youtube.com/channel/UCwXdFgeE9KYzlDdR7TG9cMw) (尤其是[无聊秀系列](https://www.youtube.com/watch?v=vqPG1tU6-c0&list=PLjxrf2q8roU3ahJVrSgAnPjzkpGmL9Czl))、[中](https://medium.com/flutter)、 [@flutterdev](http://twitter.com/flutterdev)
*   MTechViral [Youtube](https://www.youtube.com/channel/UCFTM1FGjZSkoSPDZgtbp7hA) ， [@imthepk](http://twitter.com/imthepk)
*   Flutter 社区[媒体](https://medium.com/flutter-community)， [@FlutterComm](http://twitter.com/FlutterComm)
*   r/FlutterDev [Reddit](https://www.reddit.com/r/FlutterDev/) 和
*   其他国际英语社区和本地/非英语在线/离线社区

最后，标记为[的签出问题也是一个好的包](https://github.com/flutter/flutter/labels/would%20be%20a%20good%20package)，并实现它。祝贺🎉，你现在是一个 200 字以内的颤振大师😆

### 使用颤振的缺点是什么？

正如演讲后面提到的:这是 13 个最受欢迎的 Github 问题

[![](img/e5aa1cb41a5265c1a6c680655b36d948.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wcaEJz3J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A9Hscm_UkezZTBCOx)

<figcaption>13 期中有 1-6 期超过 100 期👍</figcaption>

[![](img/af9b2a48c7eddf4ce58adf8eda310bbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_CEFDNSZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2APsnpkiE7_meHupwg)

<figcaption>13 期中有 7-13 期超过 100 期👍</figcaption>

[![](img/68a278dfaabe59d475f2578df4db1c0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qpipKSqT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AMxSeSsMjJdDdEXff) 

<figcaption>来源:[https://www . Reddit . com/r/FlutterDev/comments/7k fit 2/dart _ kot Lin/](https://www.reddit.com/r/FlutterDev/comments/7kfit2/dart_kotlin/)</figcaption>

[![](img/4f7df6639cc87ef446cbbd14b00f2c12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kfavlT6G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A3tRwreifIZKWbSGR) 

<figcaption>来源:[https://www . Reddit . com/r/FlutterDev/comments/7k fit 2/dart _ kot Lin/](https://www.reddit.com/r/FlutterDev/comments/7kfit2/dart_kotlin/)</figcaption>

简而言之:

*   ObjC < Java < Dart < Swift/kot Lin；Dart 缺乏空安全性、LLVM 优化、尾部递归、“when”等。
*   缺少 iOS 位代码→否🍎⌚️
*   没有代码推送/热更新(但对我来说没有任何意义；对于那些需要这个的人，请考虑 PWA，NativeScript 或 ReactNative，也可以在这里看到关于 PWA，ns，RN 的缺点)
*   第一方插件是用 ObjC/Java 写的(我希望它们是用 Swift/Kotlin 写的)
*   这 13 个问题中的其他常见问题可以通过变通方法解决

### 大型银行企业的 Flutter 准备得怎么样了？

(在我做了更多的研究后，这个答案在演讲后被修改了)

Flutter 已经被大型企业用于电子商务、社交媒体应用的娱乐。我还不知道 Flutter 中有任何已知的银行应用程序，最接近的可能是 [JD Digits](https://www.crunchbase.com/organization/jd-finance) ，原名 JD Finance，B 轮金融科技公司，总投资 50 亿美元。你可以在 https://flutter.dev/showcase 的和 https://itsallwidgets.com/的看到更多。

也就是说，目前，我自己不得不忍受一个蹩脚的 UI——愚蠢的网络/混合应用😭来自世界上最大的银行之一(不想提及银行名称，但我认为它来自香港或上海😉)，所以没有比那更糟糕的了。无论如何，对于一个移动银行应用程序，安全性(保密性，完整性，可用性)是最重要的方面，在我看来，我们有所有 3 个绿色复选标记。

#### 保密性

保密性可以通过数据加密来实现。

对于动态数据加密，由系统负责(主要通过 HTTPS 或 TLS)，因此每个解决方案都是等效的。

对于静态数据加密，它也由操作系统负责，但开发人员必须知道如何打开它(例如使用沙盒/加密环境来存储本地数据，使用用户密码作为本地数据的加密密钥，禁用备份)。

对于正在使用的数据，Dart 是一种现代安全语言，因此您的代码不容易受到一些常见的攻击，如堆栈溢出、缓冲区溢出、格式字符串漏洞、竞争条件。然而，作为一个安全的最佳实践，总是更新你的依赖，包括 Flutter SDK 到最新的稳定版本。

代码混淆对于 Dart/Flutter 来说是可能的，但是仍然需要进行彻底的测试。然而，我一直在使用一个蹩脚的银行 app(来自一家越南大银行)，我轻松地反编译了它，甚至获得了 eOTP 生成器的私有种子数据(之后我立即停止使用这家银行)。

✅保密协会

#### 正直

为了完整性，让我们谈谈应用程序签名和根/越狱检测。应用程序签名由 CLI 工具或分发平台完成，所有解决方案都是等效的。根/越狱检测由明智的银行使用(Nordea，我在芬兰时使用的银行有这个)，如果它检测到它正在根/越狱设备上运行，应用程序将拒绝启动。虽然可能无法直接检测 Dart 中的 root/jail-break，但目前，我们可以在本机端轻松做到这一点，并使用平台通道将此类信息提供给 Dart/Flutter 端。

诚信之道

#### 可用性

就可用性而言，我认为主要是关于用户认证。可以使用传统的用户名/密码/otp 进行身份验证，或者使用第三方插件 [local_auth](https://pub.dev/packages/local_auth) 通过设备生物特征进行身份验证。开发人员也可以这样做，每当应用程序回到前台，它需要重新认证。

可用性✅

### 能否在 Flutter 中集成原生框架(iOS)？

是的，使用[平台集成/平台渠道](https://flutter.dev/docs/development/platform-integration/platform-channels)。简而言之，我们将原生框架集成到 app 的原生端，然后 Dart/Flutter 端与框架(在原生端)之间的通信通过平台通道完成。你问的框架最好是逻辑/数据密集型的(即不是 UI 框架)😝，因为 Flutter 已经对大部分 UI/动画有了很大的支持，比如 [charts_flutter](https://pub.dev/packages/charts_flutter) 。

当我们将我们的应用程序与物联网设备集成时，我们实际上也是这样做的，物联网设备自带一个原生框架作为其 SDK 的一部分。

### 我们能以多快的速度在 Flutter 中构建一个自定义框架，而不是等待社区？

我假设这里的“定制框架”指的是库、插件、包等。答案是不到一周，也许，看你的需求。说明可在[开发包](https://flutter.dev/docs/development/packages-and-plugins/developing-packages)中获得。我自己或者和其他人合作过，比如 [websocket](https://pub.dev/packages/websocket) 、 [graphql](https://pub.dev/packages/graphql) 、 [uuid_enhanced](https://pub.dev/packages/uuid_enhanced) 。

在考虑构建自己的包之前，记住，*不要重新发明轮子*，先检查一下 pub.dev 和/或 Github。您可能希望使用现成且经过良好测试的包，或者直接使用可用的代码(如果您足够好的话，还可以制作一份 PR)。如果仍然没有，尽一切办法，自己开发它，并在[https://github.com/flutter/flutter](https://github.com/flutter/flutter)上提出您的用例，他们可能会将您的问题标记为 [*会是一个好的包*](https://github.com/flutter/flutter/labels/would%20be%20a%20good%20package) 而且谁知道呢，某个全新开始的人可能会帮助您完成那个包。

### 可以用 Flutter 开发游戏吗？有任何故障或性能问题吗？

😆如上一张幻灯片“哦，还有一件事”所示，我在 Flutter 中展示了 Flappy Bird 游戏的克隆版。这里有一个很好的指导[使用](https://blog.geekyants.com/building-a-2d-game-in-flutter-a-comprehensive-guide-913f647846bc)[火焰](https://pub.dev/packages/flame)、Flutter 游戏引擎在 Flutter 中构建一个 2D 游戏。 [2048](https://www.google.com/search?q=2048+flutter&oq=2048+flutter&aqs=chrome..69i57j0l5.3608j0j7&sourceid=chrome&ie=UTF-8) 也是一个流行的颤振克隆。另一个突出的 2D 游戏引擎是 [SpriteWidget](https://www.spritewidget.com/) 。

也就是说，这些只是 2D 游戏/游戏引擎(不是 3D 的)，而且非常简单。到目前为止，性能(即使是在我的蹩脚的三星 A3 2014 上)很好，但看看性能叠加，我强烈认为火焰有很大的改进空间。一个例子是，flame 还不支持 [shouldRepaint](https://api.flutter.dev/flutter/rendering/CustomPainter/shouldRepaint.html) 方法( [#110](https://github.com/flame-engine/flame/issues/110) )，也就是说即使我们不跨帧改变任何像素，整个屏幕再重画也是浪费，60 fps！

### 正在 AngualarJS 上运行的当前应用程序，计划立即到达。考虑到颤振。有什么推荐？

给 Flutter 一个机会。我过去犯的一个错误是，我认为移动应用程序开发就是编码来显示一个静态的(hello world) UI。剧透一下，实际上要多得多，包括编码、动画、自动化测试、干净的架构、剖析/基准测试([包括电池效率](https://flutter.dev/docs/testing/ui-performance#benchmarking))。尽量避免犯我犯过的错误😉

### 自定义视图怎么样？性能和可行性？

看看这篇来自 Flutter 社区的[精彩文章](https://medium.com/flutter-community/flutter-platformview-how-to-create-flutter-widgets-from-native-views-366e378115b6)。TLDR:可行性，是的；表现:能避免就尽量避免。

### 为什么 Sliver 和 TabBarView 不能很好地耦合，两者都有可滚动的内容🙁

改天再讨论这个问题😅。您可以关注我或给我发短信 [@truongsinhtn](https://twitter.com/truongsinhtn) 了解更多信息或进行讨论。与此同时，你知道吗，我们可以将 Cupertino widgets 与 Material widgets 混合使用，以实现理想的选项卡视图 UI，正如在[无聊剧集](https://youtu.be/QFmrgkIwAds?t=573)中看到的那样。

### 未来 flutter_web 会并入 flutter 吗？因此，在 1 个代码库可以部署到 3 个平台(Android，iOS，Web)

合并，可以；3 个平台，不，因为肯定比 3 个多得多😝。

记住，Flutter 的策略是统一开发体验，或者说“一次编写随处渲染”，因而一个代码库不仅仅局限于 Android、iOS、Web，还包括 Windows、Mac、Linux、 [Fuchsia](https://en.wikipedia.org/wiki/Google_Fuchsia) 、embedded。你知道吗，Google Nest Hub(原名 Google Home Hub)， [#IOxKL19](https://twitter.com/hashtag/ioxkl19?src=hash) 抽奖大奖，[有用 Flutter](https://www.reddit.com/r/Fuchsia/comments/blyutb/the_nest_hub_devices_are_built_on_fluttercould/) 打造的 UI。

> ![Tim Sneath profile image](img/0a7b41a8321f9cb12b22441c195c9e32.png)蒂姆·斯奈思@蒂姆·斯奈思![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)同样在今天，我们宣布助理团队正在使用 Flutter 来驱动智能显示平台，如 Google Nest Hub 和 Google Nest Hub Max 等产品所使用的。[#扑扑](https://twitter.com/hashtag/Flutter)[# Google homehub](https://twitter.com/hashtag/GoogleHomeHub)[#助手](https://twitter.com/hashtag/Assistant)[# io19](https://twitter.com/hashtag/io19)[#扑扑](https://twitter.com/hashtag/Flutter)各处21:02pm-07 2019 年 5 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1125868510645669888)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1125868510645669888)60[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1125868510645669888)178

### 有什么地方更容易获得 flutter widgets？

不确定我是否正确理解了这个问题，但可能是 [widget 目录](https://flutter.dev/docs/development/ui/widgets)、 [Widget gallery 安卓应用](https://play.google.com/store/apps/details?id=io.flutter.demo.gallery&hl=en)、pub.dev、GitHub、【https://material.io/develop/flutter/】T4。记住，Material 采用了 Flutter 作为一级平台。

### 颤振的简易状态管理？

有许多简单状态管理解决方案，您可能难以选择🤣。我偏好是可测试性和干净架构的阻塞和提供者。

### 如果我已经有 Angular 的经验，那么选择 ionic 作为跨平台框架不是比 Flutter 更好吗？

有了 Angular code base，你有几个选项，NativeScript，Ionic，PWA(有无可信 Web 活动)。NativeScript 类似于 RN，但用 Angular 代替 React，Ionic 和 PWA 是包装在应用程序中的 web 视图。具有可信网络活动的 PWA 只能在谷歌 Play 商店上市(不能在 iOS 应用商店上市)。

然而，如果你不打算有很多硬件通信(如音频、视频、[连接](https://developer.android.com/guide/topics/connectivity)、[传感器](https://developer.android.com/guide/topics/sensors))，并且你的造型/动画可以大部分使用 CSS 来表达，那么你应该只考虑 web/hybrid app。硬件通信在某种程度上是可能的，但是可访问性、可靠性、性能和成熟度的水平因解决方案和操作系统而异。使用 JS 制作动画是可能的，但是性能可能会以成千上万种方式出错。当 JS 动画出错时，如果没有广泛的剖析工具，就像在黑暗中拍摄一样。基本上，你需要 JS 中的疾速追杀或 TMNT 来摆脱这种困境(相信我，他们曾经这样做过😂)

### 我能在 Github 或者其他什么地方得到 Flappy Bird 克隆的代码吗？😆

抱歉，这个问题问得早了 2 分钟😝，在快速演示之后，提供了 GitHub 链接和二维码，现在再次提供:

[https://github . com/Truong sinh/fluttery _ dash/releases/tag/v 0 . 0 . 1](https://github.com/truongsinh/fluttery_dash/releases/tag/v0.0.1)

### 你用什么来串流手机屏幕？看起来很光滑

[Genymobile/scrcpy](https://github.com/Genymobile/scrcpy)

### 我应该完全避免 setState 吗？

不，当您想要封装小部件行为时，setState 有时与其他状态管理策略结合使用是很好的。但是在使用 setState 的时候，一定不要写[单元测试](https://flutter.dev/docs/cookbook/testing/unit/introduction)来验证作为一个单元的 setState 逻辑，而是要写[小部件测试](https://flutter.dev/docs/cookbook/testing/widget/introduction)(或者[集成测试](https://flutter.dev/docs/cookbook/testing/integration/introduction))。把内部状态和 setState 想象成 OOP 中的一个@private 方法。

如果 setState 的业务逻辑开始变得复杂，这可能是对重构操作的呼唤。

此外，在状态管理中有应用程序状态，这种状态管理有一个持久的机制，如果应用程序在用户流中崩溃，我们可以在应用程序重新启动时准确地恢复到那个屏幕。例如，用户登录，浏览图片，打开一个，写 200 字的评论，应用程序崩溃。如果我们有持续的状态，一旦应用程序重新打开，它将准确地出现在特定图片的评论框中，预填充 200 个单词(自动保存这么说)。

### 如果我已经有了原生应用程序，我的公司应该如何采用 Flutter？

我们自己也在这样做。我是一个敏捷布道者，我相信迭代和增量，因此我的建议是逐渐将 Flutter 嵌入到你当前的原生应用中，而不是从头开始重建。

*   [一般说明](https://github.com/flutter/flutter/wiki/Add-Flutter-to-existing-apps)
*   [一般说明的 6 个变通办法，适用于 Android](https://hackernoon.com/how-to-add-flutter-to-your-production-android-app-4b5150de9e44)
*   iOS 的一个小解决方案 [#32791](https://github.com/flutter/flutter/pull/32791)

### 有使用 admob 和报警管理器的经验吗？当我们同时使用两个插件时，应用程序会崩溃，并产生无用的转储堆栈！

抱歉，我不知道，但是请仔细检查你是否正在使用第一方插件， [firebase_admob](https://pub.dev/packages/firebase_admob) 和 [android_alarm_manager](https://pub.dev/packages/android_alarm_manager) ，按照说明操作。如果应用程序仍然崩溃，在 [Github](https://github.com/flutter/flutter) 上发布堆栈转储，社区、合作者或核心成员将会看到。这可能是一个 bug，报告 bug 是导致 Flutter 的一种方式。

### 后记

我很荣幸被邀请作为演讲人，这是一段美好的回忆。我对与会者的兴趣程度感到惊讶，尤其是在活动中收到赞助商代表的提问时。衷心感谢 [GDG 吉隆坡](https://www.gdgkl.org/)和[双威科技俱乐部](https://www.facebook.com/sunwaytechclub/)出色的组织者，谢谢你们，赞助商。但最重要的是，感谢各位与会者；没有你，这个事件就不会发生。

[![](img/05c50ae801e83a0aa225ee0d32d997c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sVqXdqKn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Amdbbk07vmyq_9he5DW7cUQ.jpeg) 

<figcaption>“我知道这已经是我的第 7 个问题了，所以我保证这是最后一个”，“哦不，我其实很高兴你感兴趣。即使你现在没有问题了，以后也可以随时联系我”</figcaption>

这篇文章是免费的，你的拍手也是免费的👏。你知道你可以按拍手键吗👏按钮 50 次？

[Flutter 社区(@FlutterComm) | Twitter](https://twitter.com/FlutterComm)

* * *