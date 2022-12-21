# Android Studio vs Xcode vs AppCode:关于编码速度的简单比较

> 原文：<https://dev.to/chicio/android-studio-vs-xcode-vs-appcode-a-brief-comparison-about-coding-speed-9m4>

在这篇文章中，我将比较 Android Studio、Xcode 和一些 JetBrains IDEs 在代码创建和重构方面可能达到的编码速度。

IDE，*集成开发环境*，是软件开发者工具箱。当我开始在 lastminute.com 集团[工作的时候，我对 Android 平台的了解非常有限。但是...](https://lmgroup.lastminute.com/)[lastminute.com 集团](https://lmgroup.lastminute.com/)是一家[敏捷软件开发](https://en.wikipedia.org/wiki/Agile_software_development)公司，我们在开发工作流程中使用的技术之一是[结对编程](https://en.wikipedia.org/wiki/Pair_programming):两名开发人员在同一个工作站上处理相同的功能。
据维基百科报道，结对编程的主要优势之一是知识共享:

> 无论是在行业中还是在课堂上，结对编程者之间不断地共享知识，许多来源表明，学生在结对编程时表现出更高的信心，许多人从编程语言规则的技巧到整体设计技能都学到了知识。在“混杂配对”中，每个程序员与团队中的所有其他程序员交流和工作，而不是只与一个伙伴配对，这导致系统的知识在整个团队中传播。结对编程允许程序员检查他们伙伴的代码，并提供反馈，这对于提高他们自己为自己的学习活动开发监控机制的能力是必要的。

这就是为什么我开始与我的同事 [Francesco Bonfadelli](https://www.linkedin.com/in/fbonfadelli/) 合作，他是一名资深的 Android、iOS 和后端开发人员。在我们的结对编程课上，我学到了很多关于为 Android 平台开发移动应用的知识。我在最初几天学到的一件事是官方 ide 之间的区别:Android Studio 和 Xcode。在看到 Francesco 在 Android 编码会话期间能够实现的编码速度，以及在 iOS 的 Xcode 中做同样的事情要慢多少之后，我意识到 Android Studio 及其重构功能与 Xcode 相比要先进得多。

在这篇文章中，我将简要分析一些常用于移动应用程序开发的 ide，重点是通过使用它们可能达到的编码速度，并且我将向您解释为什么在撰写本文时，我开始更喜欢 JetBrains IDEs 家族(不仅仅用于移动应用程序开发:bowtie:)。

### Xcode

我一直很喜欢 Xcode。我在 8 年前开始使用它，在我的日常工作中它仍然伴随着我。它在几秒钟内打开，你可以很快开始编码。但是....当你的应用程序代码开始变得越来越复杂，而你需要做一个简单的重构操作时，会发生什么呢？当它需要创建一个新的类/属性时，对你有帮助吗？当你需要在代码中导航，需要从一个类快速跳到另一个类时，它对你有帮助吗？好吧，说实话这对你没多大帮助。甚至一个简单的重命名都可能成为一个痛苦的操作，特别是如果你有一个混合了 Swift/Objective-C 部分的项目。一切都必须手动完成。例如，考虑混合代码创建/重构操作的列表:

*   创建一个新类
*   实例化它并将其作为局部变量保存
*   向前一个类添加一个方法
*   向先前创建的方法添加参数
*   提取局部变量作为我在其中创建它的控制器的属性

在下面的视频中，我将尝试用 Xcode 做这些操作。在撰写本文时，可用的 Xcode 版本是 9.2。

[https://www.youtube.com/embed/tsuS8UoSS1A](https://www.youtube.com/embed/tsuS8UoSS1A)

**2 分钟以上实现以上列表中的所有东西。**
真慢，不是吗？！？？😨

### 安卓工作室

在 lastminute.com 集团之前，我只在一些非常简单的 Android 应用程序上使用过几次 Android Studio。然后我开始和[弗朗西斯科](https://www.linkedin.com/in/fbonfadelli/)一起工作，他向我介绍了 JetBrains IDEs 的力量。这个 IDE 使您能够在源代码中快速导航，创建和修改类，并允许您在不离开键盘的情况下进行许多其他重构操作！基本上你可以写代码，忘记你的鼠标！！😮。您可以在开发流程中使用的键盘快捷键列表是无穷无尽的。你可以在这里找到完整的名单。让我们试着做和我之前用 Xcode 做的完全一样的操作，我还添加了一个在之前所有操作结束时创建的类的重命名。在撰写本文时，可用的 Android Studio 版本是 3.0.1。

[https://www.youtube.com/embed/Xp3v9VsgFjw](https://www.youtube.com/embed/Xp3v9VsgFjw)

只有 50 秒，我完成了所有的工作(而且我没有使劲敲键盘...😜).
如你所见，Android Studio 赋予你以光速编写代码的能力！！！😳。

### AppCode

可以想象，在使用 Android Studio 几个小时后，我开始想知道是否有一个 IDE 可以让我设置相同的编写代码风格和工作流程。我的另一位同事，资深 iOS 和 Android 开发者 Tommaso Resti 第一次向我展示了 AppCode。这是 JetBrains 为 iOS 开发的另一个 IDE。它允许您使用 Android Studio 中的一些重构工具，从而提高开发速度。然而，在这种情况下并不都是和平与光明。一些重构工具
不适用于 Swift，你仍然需要 Xcode 来处理 Xib 和 story board(JetBrains 团队为 interface builder 开发了一个插件，但已经停止使用)。不管怎么说，如果你开始习惯 Android Studio 编写代码的工作流程，你就会对 AppCode ☺️.有宾至如归的感觉

### 最后的想法

Android Studio 和 AppCode 基于 JetBrains 著名的 Java IDE IntelliJ IDEA。但这只是故事的一半:JetBrains IDE 家族确实很大。您可以为您最喜欢的每种语言找到一个 IDE:

*   CLion，用于 C 和 C++
*   PhpStorm
*   皮查姆
*   Ruby 的 RubyMine
*   GoLand for GO
*   C#的附加条款

所以不要担心:如果你想开始提高你的编码速度，可能有一个你最喜欢的语言的 IDE。Xcode 在我心中永远有一个特殊的位置。作为一名移动开发人员，我仍然会在日常工作中继续使用它。但是...我用 JetBrains IDEs 获得的编码速度不能被忽略😈。这就是为什么我开始更喜欢他们❤️.

*原载于[https://www . fabrizioduroni . it](https://www.fabrizioduroni.it/2018/01/16/ide-refactoring-android-studio-xcode-appcode-webstorm-jetbrains.html)2018 年 1 月 3 日。*