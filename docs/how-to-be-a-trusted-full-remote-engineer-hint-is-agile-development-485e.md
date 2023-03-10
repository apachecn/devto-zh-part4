# 如何做一个值得信赖的全远程工程师？提示是敏捷开发。

> 原文：<https://dev.to/yuno_miyako/how-to-be-a-trusted-full-remote-engineer-hint-is-agile-development-485e>

# 背景

全远程工程师就很好了。你可以在任何地方工作，所以在国外旅行时，你可以随时工作。支持这种美妙自由的是我们客户的信任。
全远程作业只能由产品来判断，因为他们看不到你作业的过程。
作为一名全职远程工程师，向客户传递价值、获得客户信任以及建立良好的关系是生存的必要条件。

# 我需要怎么做才能获得信任？

如何才能传递价值，获得信任，并与客户保持良好的关系？我认为有很多因素，但我认为以下三点很重要。

*   每周显示你的结果
*   高质量
*   对反馈反应迅速

敏捷开发方法可以通过以下方式来应对以上三个问题。

*   从最高优先级的故事开始开发
*   测试驱动开发
*   定期重构

为了详细解释每一个，我想解释如何按照下面的顺序进行敏捷开发。

订单:二手书店的老板要求您开发一个可以管理库存并允许客户检查库存的 web 应用程序。店主可以登记购买的图书，他想立即知道库存数量。他的客户可以访问该网站，了解有什么书库存，卖多少钱，并按书名搜索。我觉得如果能看到其他卖的好的产品，从浏览历史中推荐推荐的产品是最好的。

## 每周显示你的结果→从最重要的故事开始

你听从了这个主人的要求，决定开发所有的功能。你会告诉业主，开发需要 3 个月，你马上开始工作。老板看不到你是如何工作的。所以如果你一两个月没把工作做完，他会很焦虑，说:“怎么样了？”
你会回答，“我做了详细设计，设置了 DB，写了模型和控制器。我也对它们进行了适当的测试。接下来，我将编写视图并完成它。我一个月就能搞定。”你会给他看源代码。
但是他并不知道它是否真的接近完成，因为他看不到正在移动的作品。不能说你和他之间存在信任关系。

你用敏捷做什么？确定最重要的故事，并在此基础上展开。简单地说，这个故事是“端到端工作的特性”。
顶层故事是故事列表中最重要的价值故事(特征)。
在这个例子中，“我可以登记账簿并以列表方式查看库存”是核心价值。没有这个你什么也做不了。

在敏捷中，我们开始创建只有这个特性的产品。
因为功能限定一个，所以一周就能搞定。
给主人看一个一周后就要搬家的产品。主人可以确认这本书可以注册，所以他一定很高兴知道进度，而不是看源代码。
在接下来的一周，我们将实施下一个重要的故事，并让他看到下周正在移动的产品。所有者可以看到产品每周添加的功能，因此他可以放心地将工作交给您。

# 高质量→测试驱动开发

所有者现在很高兴确认这本书可以注册，但他注意到注册空白书名会导致一个 bug。还有很多其他的 bug，比如可以在价格里放文字，或者如果他点击两次就会被双重注册。你用了整整一周的时间来编写测试和修复 bug，但是从一周前开始就没有添加任何特性。你会失去主人的信任。

无论你交付的产品有多快，低质量的产品都会失去客户的信任。

你用敏捷做什么？敏捷开发基本上是用测试驱动开发来完成的。测试驱动开发是一种从开发开始的方法，通过从测试开始编写并实现它以使测试通过。重要的是做一个最小的实现来通过你正在编写的测试。
测试驱动开发可以保证书面测试的质量，也可以避免过度质量(过多的实现)。
因为测试驱动开发很深入，所以我希望你想尽一切办法学习其他材料。

# 快速响应反馈→重构

每当你看到一个产品每周移动一次，一定要得到反馈。当我试图触摸移动的东西时，越来越多的请求在桌上讨论时没有出现。
比如“因为一个月有 100 多本书，所以注册越容易越好”或者“如果还附上亚马逊的链接，我们房子的价格可能看起来更合理”。
所有这些反馈都是故事。放入故事列表，我们将从最高优先级的故事开始开发。
反馈越多，即修正越多。
重构对于敏捷地处理功能添加和修改是必不可少的。

重构的最佳时机是什么时候？一直都是。
准确的说，在做测试驱动开发的同时，我们总是在通过测试的时候花时间进行重构。
正是这种作为流动。

测试->实现->重构

如此反复，创造出一个产品，这个产品就耐变了。当主人看到他提出的要求时，他会立刻增加，他对你的信心也会增加。因此，一定要有一个代码库，能够快速地适应客户的需求。

# 终于

敏捷开发这个词是流行语，但我认为很少有人在研究具体到什么样的方法和如何进行。我想你可以通过这个例子看到敏捷如何更好地传递价值。
这次有很多东西没有报道，请搜索敏捷开发或者 Scrum 开发这个词，获取信息。

那么，祝你工程师生涯愉快。

PS。我正在寻找远程工作。我专攻 iOS app (swift)，Web app (React，Vue)，API 开发(Python，Typescript，Java)。我的优势在于敏捷地传递价值。
联系人:mugiroki07[@]gmail.com