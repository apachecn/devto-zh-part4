# 为什么#无服务器正在吞噬堆栈

> 原文：<https://dev.to/dashbird/why-serverless-is-eating-the-stack-117>

在当今的技术和商业环境中，**以**加速**的步伐不断变化**，保持**的敏捷**至关重要。

软件开发行业一直在构建令人惊叹的工具，并为开发人员配备我们需要的东西来面对这些挑战。

但是实现这些工具中的一些，无论它们是有形的(例如开源项目)，还是抽象的(例如编程或架构模式)，有时会被证明是一项艰巨的任务。

[![Man climbing ice wall](img/48e2626ba20697423d87dffb2a4adf32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8tOAfvE1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wn8h48qjlf403titox28.PNG)

采用微服务方法。仅在架构层面上实现它就已经是一个**的挑战**。你还有很多其他的事情要考虑...

蓝色/绿色还是淡黄色？还是滚动部署？

*如何解耦服务？如何将服务保持为独立的组件？*

*服务发现？如何编排服务？还是应该允许他们独立编舞？*

*要 GraphQL 还是不要 GraphQL？*

# 你明白了。简直是**压倒**。

[![Man screaming](img/73eaf8d60eff42e203f29d901c63cce8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YgHmZd8w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3l4oal61hnxklfpzzkar.jpg)

现在考虑一下，我们不可能总是有一个+100 人的团队负责前端、后端、开发运维、测试、UI/UX、代码审查等。

### **大多数开发团队都很小**！

不到十几个软件战士...

你仍然希望有这样一个问题，即**负责**T2 的 1，000，000 台服务器最终会辜负他们的承诺...上菜？

好吧，如果你意识到许多开发人员、团队领导和 CTO 已经做了什么，**你想把这些问题抽象出来**。*除非你有足够的钱雇佣最好的 DevOps 团队。*

以下是**无服务器堆栈吞噬云**的一些原因:

# 成熟的开发者工具

[![Tools on wooden panel](img/cd08a457744d7c813b89aa52cf5700a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aXiLhkrh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1b8z4x6o0qb1glptwt3s.PNG)

直到最近，无服务器计算仍处于早期阶段，开发人员工具还不成熟。这最终增加了风险，耗尽了该平台承诺的很大一部分生产力收益。今天，这不再像过去那样是一个问题。

从 **[框架](https://geekflare.com/serverless-apps-frameworks/)** 到 **[集成](https://docs.aws.amazon.com/lambda/latest/dg/lambda-services.html)** 到 **[监控/调试](https://dashbird.io/blog/top-serverless-monitoring-tools/)** ，市场现在提供了广泛而丰富的工具供开发团队选择，让技术领导者放心地依赖无服务器。

例如， [AWS 无服务器应用程序模型](https://aws.amazon.com/serverless/sam/)有一些实用程序可以**自动执行与开发和部署无服务器功能相关的大部分重复和手动任务**。

大多数无服务器产品现在**与数十种其他云服务**集成，允许一个完整软件生命周期的**快速实施**，**连接计算到对象存储到数据库到消息队列**等等。在监控和调试方面，我们现在有专门用于无服务器的成熟服务，让我们不必担心。

# 快速上市

[![Horse race](img/d69cd657382b54518c5591e5154b620e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DCtKdRQd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hwxgx5vhln2d57x2zq50.PNG)

时间可以说是现在软件开发项目面临的**主要压力点**之一。客户，无论是内部的还是外部的，通常都非常关心尽快交付一个应用程序、特性或补丁**，只要它**满足需求**并以**稳定且有弹性的**方式实现。**

通常，团队管理时间的方法只有两种:

1.  **雇佣更多开发人员**增加开发吞吐量；
2.  **简化**实施到**保持质量&成本**同时**降低交货期**；

随着软件开发人员劳动力市场的压力和**工资的增加**，第一条在许多情况下并不是一个真正的选择，无论是因为财务限制还是因为没有可用的人来雇佣。

首席技术官们发现，**无服务器通过简化基础设施的规划、部署和管理，对第 2 项做出了巨大贡献。**

例如，AWS Lambda 等无服务器功能可以扩展到 3000 个并发执行，如果需要，这个阈值可以很容易地增加到无穷大。所有这些都不需要设置一台服务器或负载平衡器。

**实际上，在部署应用程序**之前，团队需要考虑的所有基础设施方面的事情**——选择正确的机器、配置、负载平衡、扩展等——都被无服务器**抽象化了。它大大简化了工作，并有可能节省几十个小时，如果不是几百或几千个小时的话。

这种简化不仅节省了时间，减轻了项目负责人的交付压力，而且对于那些依赖于渴望获得无服务器优势的团队的公司来说，这也使得 T2 能够快速上市。从长远来看，**这些公司通过比竞争对手更快地传递价值和适应市场趋势，将在各自领域占据主导地位。**

# 关注重要的事情

[![Focus lens](img/567b33a3bd092d4fdace3ed5ef139554.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6oWL_8LQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/60bjhpcto46i6fwpelwq.PNG)

拥有这个街区最好的 DevOps 团队曾经是大多数技术项目的一个优势。如果一个软件不能以高性能的方式运行和扩展，那么它是否能够提供最佳价值就无关紧要了，这需要久经考验的基础架构规划和管理技能。因此，软件团队需要投入大量的资源(人力、时间、资金)来确保基础设施问题得到适当的解决。

无服务器颠覆了这种逻辑，团队正在快速适应。通过抽象出关于创建一个在云中运行的软件平台的大部分问题，开发团队现在可以专注于真正重要的事情:他们的客户需求。这对软件团队和用户来说都是好事。

### 想一想:在 Amazon.com 购物的人根本不关心电子商务使用的技术或平台。

他们真正关心的是在网站上有很好的体验，能够快速搜索和找到相关的项目，等等。这些都是开发人员正在寻求利用他们的资源的事情。例如:

*   如何实施软件来改善用户体验？
*   我们能否整合人工智能，让我们的应用更智能？
*   哪些实践将允许我们减少错误并提高整体软件健康？
*   我如何管理我的团队，让他们在工作中更快乐、更有效率？

随着时间的推移，您的团队可以开始考虑这些问题了。

# 久经考验

[![Cannon shooting](img/914987e6ead1ec53336ee84213364395.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--41_JWEIl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aac341tcxird1wqj8z7a.PNG)

各种规模的成千上万家公司已经使用无服务器很多年了，**在市场上最严格、最恶劣的环境中测试架构**。从大型企业到小型企业和初创公司，无服务器模式已经过无数次测试，T2 已经无数次证明了它的价值。

最重要的是，无服务器已经被所有行业采用:从可口可乐这样的 T2 食品饮料公司，到网飞这样的数字公司，再到诺德斯特龙这样的零售商。

# 低成本

尽管许多人认为无服务器可能比传统的服务器集群更昂贵，但在大多数情况下，当我们查看真实数据时，这种想法并不成立。

[![Pennies in a jar](img/8f3c3294275b9fd18b0f1c245534e17c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mR49y-vE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cj5ms1740vowjzkjsp7l.PNG)

### 我见过 [serverless 居然砍掉 90%一个云账单的案例](https://www.linkedin.com/pulse/how-serverless-can-save-100000-your-cloud-bill-renato-byrro/)！

这是因为无服务器不收取闲置或保留资源的费用。为了匹配服务器集群中无服务器平台的弹性和可靠性，需要投入大量资源来适应需求的波动，这导致了浪费。

当我们考虑到基础设施抽象所节省的工作时间时——尤其是考虑到 DevOps 团队的成本会有多高——经济效益甚至更大。

# 你和你的团队怎么样？

您准备好享受无服务器带来的好处了吗？如果你想了解更多关于如何在你的项目中更好地利用无服务器的优势，[在这里下载我的免费电子书](https://dashbird.io/serverless-benefits/)。

* * *

### *照片致谢:*

*   *封面图片:[飞盘](https://unsplash.com/@boei?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [无刷](https://unsplash.com/s/photos/agile?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*
*   *男子爬冰墙:[罗伯特·贝克](https://unsplash.com/@vegasphotog?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/s/photos/hard?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*
*   *男人尖叫:[股票摄影](https://unsplash.com/@aplaceforcreation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/s/photos/screaming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*
*   *赛马:[朱莉亚·乔皮恩](https://unsplash.com/@vitreous_macula?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/s/photos/horse-race?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*
*   *木制面板上的工具:[托德·夸肯布什](https://unsplash.com/@toddquackenbush?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上的[Unsplash](https://unsplash.com/s/photos/tools?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*
*   *聚焦镜头:[保罗·斯科鲁普斯卡斯](https://unsplash.com/@pawelskor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/s/photos/focus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*
*   *大炮射击:[纽约公共图书馆](https://unsplash.com/@nypl?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/photos/TNU8604rMVk)T5】*
*   *罐子里的便士:[迈克尔·朗米尔](https://unsplash.com/@f7photo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[Unsplash](https://unsplash.com/s/photos/pennies?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5】*