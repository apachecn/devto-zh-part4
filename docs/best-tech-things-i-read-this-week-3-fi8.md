# 本周我读过的最好的科技文章-第三

> 原文：<https://dev.to/mohanarpit/best-tech-things-i-read-this-week-3-fi8>

*我分享 TL；我每个工作日通过简讯阅读的软件工程文章的 DR 版本- 注册，在你的收件箱里收到这些。*

* * *

*#工程#心态*

### [注重产品的软件工程师](http://bit.ly/product-engineer)

> “作为一门年轻的学科，我们已经花了很多时间研究‘如何’构建软件，这仍然是学校的一大焦点。但是一旦你有了基础，你就需要那些积极参与“为什么”的开发人员。渴望使用技术跨越人类/用户问题的工程师。那些有同理心的人想要获得神奇的体验。在我的书中，这就是产品工程师的定义。糟糕的产品工程师偷工减料太多，但优秀的工程师知道，在构建阶段，最少的可爱产品需要正确的深度。”

*-Shopify 首席技术官 Jean-Michel le mieux*

这篇文章分享了以产品为中心的软件工程师的 9 个关键特征，以及软件工程师可以做的 6 件事来建立产品思维:

1.  了解你的公司是如何以及为什么成功的
2.  与你的产品经理建立牢固的关系
3.  参与用户研究、客户支持和其他活动，在这些活动中，您可以了解更多关于产品如何工作的信息
4.  提出有充分依据的产品建议
5.  为你从事的项目提供产品/工程权衡
6.  经常向你的产品经理寻求反馈。

依我看，采用这种思维方式可以帮助所有工程师对我们正在开发的产品有更深的理解，反过来，帮助我们成为更好的工程师，开发更好的产品。

*8 分钟读取*

* * *

*#架构#基础*

### [软件架构的 5 个关键原则](http://bit.ly/architecture-principles)

帮助软件架构师做出有效决策的 5 个软件架构原则:

**1。坚实的原则**
*a)单一责任原则* :
每个系统能力(例如服务/模块/API)应该只有一个责任

*b)开闭原则* :
最好是扩展一个系统行为，而不是修改它。

*c)利斯科夫替换原理* :
给定两个具有相同契约的分布式组件，其中一个应该可以被另一个具有相同契约的组件替换，而不改变系统的正确性。

*d)接口分离原则* :
接口/契约必须尽可能的细粒度，并且是特定于客户的

*e)依赖倒置原则* :
高层模块不要依赖低层模块；它们都应该依赖于抽象。

**2。最小惊讶原则**:利用用户已有的知识来最小化他们在使用模块时的学习曲线

**3。最省力原则**:每个人都倾向于走尽可能不费力的路。因此，通过将正确的架构&期望放在适当的位置来瞄准一个强有力的开始。

**4。机会成本原理**:一个选择的机会成本就是我们为了得到它而放弃的东西。为了做出一个好的经济决策，我们希望选择对我们最有利但成本最低的方案。

**5。最后负责的时刻原则**:不要过早做出决定，而是推迟承诺，让重要的、不可逆的决定公开，直到不做决定的成本大于做决定的成本。

*7 分钟读取*

* * *

*# python #生产力*

### [Dropbox 的类型检查之旅 400 万行 Python](http://bit.ly/dropbox-typechecking)

这篇文章详细分享了 Dropbox 团队将代码迁移到静态类型检查的基本原理；团队在整个过程中面临的挑战&他们如何应对这些挑战；他们内部采用的各种方法；以及它对工程团队生产力的影响。说服团队全力以赴的几个好处是:

*   类型检查器会发现许多细微(也不那么细微)的错误
*   重构要容易得多
*   类型检查提供了快速反馈，并允许更快地迭代。
*   没有必要编写脆弱、难以维护的单元测试来模拟和修补世界以获得快速反馈。
*   ide 和编辑器(如 PyCharm 和 Visual Studio Code)利用类型注释来提供代码完成、突出错误和支持更好的转到定义功能

这是一个很好的案例，说明了 Python 中的静态类型检查对于大规模项目的用处。

*20 分钟读取*

* * *

*#编程#文化*

### [结对与代码评审:比较开发人员文化](http://bit.ly/pair-code-review)

这篇文章是 Paul Hinze 在 6 年前写的，当时他换了工作，从结对编程的开发文化过渡到了同行代码评审的文化。他对这两种做法持平衡的观点。

在**结对**中，团队中的每个人因为不断的交流而一起变得更好。初级开发人员可以通过给他们一对有经验的人来轻松培训；最佳实践、知识、新工具和技术自然地在团队中迅速传播&。然而，配对并不总是阳光和彩虹。这可能会导致核心人才被过度稀释，从而扼杀生产力和士气。当涉及到解决系统设计和架构的更大问题时，需要更深入的思考或创造力，结对可能不利于进步。

在遵循**代码评审**实践的团队中，有一种为评审做好工作的激励压力。深入思考问题没有障碍。由于代码审查是异步的，所以工作安排有很大的灵活性。代码审查可以战略性地分布，以确保更有经验的开发人员总是接触某些类型的代码审查，这提供了额外的质量控制和对产品中出现的错误的保护。然而，在有代码审查过程的团队中，你可能会感到孤独，长时间地走神，并且在截止日期前迟到。如果代码评审没有以紧凑的节奏完成，它们可能会成为瓶颈

*10 分钟读取*

* * *

*#解题#学习*

### [程序员的纪律](http://bit.ly/programmer-discipline)

Sidu Ponnapa 职业生涯的大部分时间都在攀登“技术的人的一面”。在过去的 4 年里，他帮助 GojekTech 从 100 万 GMV 发展到 100 亿。在这篇文章中，Sidu 阐述了团队领导&决策者如何摆脱程序员的纪律来建立&规模有效的组织。编程本来就难。**程序员在为难题构建解决方案时，面临编程固有的挑战——模糊性、复杂性、集成性和矛盾性。**为了降低问题的难度，程序员将事情分解成“系统”，并通过“规程”定义他们如何参与这些系统。当处理业务扩展中固有的问题时，这些学科中的许多都非常适合。因为所有有能力的程序员都有设计、构建和运行大型复杂系统的经验，所以在开发非技术系统时，他们是优秀的共鸣板。

*8 分钟读取*

* * *

我分享了我们在 [Appsmith](http://bit.ly/2Zdv9GS) 的团队一周以来阅读的最佳科技文章列表。

*你可以在这里找到最后两个[，在这里](http://bit.ly/first-nl)找到[。希望听到对此的反馈，并知道你在读什么？](http://bit.ly/second-list)*