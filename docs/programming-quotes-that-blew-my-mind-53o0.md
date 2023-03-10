# 令我震惊的编程语录

> 原文：<https://dev.to/jorgemanrubia/programming-quotes-that-blew-my-mind-53o0>

我喜欢改变我的世界的想法。它们通常来自书籍、文章和演讲，但在一些罕见的情况下，这只是一个简单的引用就能产生这种效果。

这种引用的影响很大程度上取决于你的背景和上下文。事后看来，这里有三个例子对我产生了巨大的影响。

## 编程习惯

> 我不是一个伟大的程序员；我只是一个习惯很棒的好程序员。

作者肯特·贝克，马丁·福勒在《重构》(第一版)中引用，1999 年 6 月

当我在 21 世纪初阅读《重构》一书时，这句话引起了我的注意。在那个你经常听说编程是真正工程师的二等活动的时代([愚蠢，有些人今天还在延续](https://twitter.com/CPITIA/status/1172801299886333953))，像这本书、 [XP 解释](https://www.amazon.com/Extreme-Programming-Explained-Embrace-Change/dp/0201616416)或者[实用程序员](https://pragprog.com/book/tpp/the-pragmatic-programmer)把重点放在了编写更好更健壮代码的实践上。这句话让我想了解这些:自动化测试、可读代码的重要性、实现模式、重构、持续集成等等。

最重要的是，这句话让我尊重编写代码的技巧。在那些日子里，我的教授告诉我们，我们的工作应该是绘制全面的图表和编写长文档，让其他人编写代码。想象一下曾经关注过那些！我仍然对那个时代的*工程师*这个术语怀有一种微妙而不合理的厌恶。

## 反馈回路

> 作为一名工程师，你应该不断努力使你的反馈循环在时间上更短和/或在范围上更广

肯特·贝克，2014 年 11 月

这句话给一个我从未想到的概念起了个名字:*反馈回路*。今天，我相信它是开发者快乐的一个关键因素。它指的是编写一些代码并验证其效果的循环。在编写任何代码时，你都会无数次地经历这个循环，所以你最好注意它是如何展开的。

反馈循环通常与自动化测试相关联。您希望您的测试快速(时间更短)并且[尽可能多地测试真实的东西](https://www.jorgemanrubia.com/2018/05/19/on-rails-testing/)(范围更广)，这通常是相反的属性。事实上，拥有一个有效的反馈循环是编写测试的一个极好的理由，尽管这并不总是可能或合适的。

我认为测试只是问题的一个方面。以下是一些不同的想法:

*   改善反馈回路值得投入时间。例如，我[创建了这个](https://www.jorgemanrubia.com/2019/07/07/invoke_the_interactive_brokers_api_from_ruby/)来避免因为启动时间慢而不得不使用 JRuby。出于同样的原因，这个命令行工具也被[用于另一个系统。](https://www.jorgemanrubia.com/2017/11/26/testing-a-command-line-tool-with-plain-ruby/)
*   快速的加载时间对于用户来说是一个至关重要的特性，但是对于编程的快乐来说也是必不可少的。加载缓慢的应用程序会很快耗尽你在开发时的快乐。
*   REPL shell 是在许多场景中进行实验和故障排除的绝佳工具。即时反应构成了一个很好的反馈循环。
*   在做性能工作时，尽可能自动化您正在使用的任何机制。在我最近做的一些评测工作中，只需运行评测器，并通过击键在浏览器中自动打开生成的报告，就能显著改善我的体验。
*   说到用户界面。现代浏览器开发工具为直接操作 HTML 和 CSS 提供了极好的机制。React 和 Vue 中的热重装太棒了，就像[我不喜欢温泉场景](https://medium.com/@jmanrubia/escaping-the-spa-rabbit-hole-with-turbolinks-903f942bf52c)一样。还有 [SwiftUI](https://developer.apple.com/xcode/swiftui/) 看起来很惊艳。一个即时的反馈循环对 UI 工作来说是如此方便！

## 范围和期限

> “固定的期限，可协商的范围”是产品管理中最被低估的模式。这是运输的秘密。

瑞安·辛格，2015 年 10 月

当我第一次读到它时，这个想法让我大吃一惊。在那之前，我已经开始认为软件中的期限是负面的。我从来没有想到它们是一种缩小范围的机制。它成功了，因为这解决了我非常熟悉的两个问题:

*   错过最后期限是因为在周期开始时的粗略估计总是错误的，不管你如何缩短迭代。
*   不知道一个项目何时完成，因为总是有看起来如此重要的改进。

[编程是一个棘手的问题](https://blog.codinghorror.com/development-is-inherently-wicked/):你不明白这个问题，直到解决了它之后的*。如果允许您修改范围，您可以随着项目的进展调整未完成的工作量，这样就能遵守截止日期。而且你也有一个机制来决定项目什么时候足够好，什么时候发布。*

我认为这是那些虽然简单，但很难成功实施的想法之一。如果你感兴趣，这本书解释了他们在 Basecamp 是怎么做的，这是我很久以来读过的最好的书之一。

我很乐意听到影响他人的类似言论。