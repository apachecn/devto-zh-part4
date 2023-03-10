# 本周我读过的最好的科技文章-第四

> 原文：<https://dev.to/mohanarpit/best-tech-things-i-read-this-week-4-4l9j>

*我分享 TL；我每个工作日通过简讯阅读的软件工程文章的 DR 版本- [in.snippets()。在这里](https://mailchi.mp/appsmith/insnippets?utm_source=devto&utm_medium=post04&utm_campaign=is)注册，在你的收件箱里收到这些。*

* * *

### [亚马逊网络服务 10 年的 10 个教训](http://bit.ly/10LessonsAWS)

亚马逊首席技术官沃纳·威格尔在三年前 AWS 完成 10 年时写了这篇文章。在我不断思考&在我的创业公司做出工程选择的时候，这些是需要遵循的一些很好的基本原则。

**1。构建可进化的系统:记住未来的可能性。构建一个架构，在这个架构中，您可以引入新的软件组件，而无需关闭服务。
2**。期待意想不到的**:随着时间的推移，一切最终都会失败。许多故障场景在设计和构建时是未知的。建立将失败视为自然现象的系统。
**3。原语不是框架**
**4。自动化是关键**
**5。API 永远是**:设计 API 是一项非常重要的任务，因为你只有一次机会把它做好。
**6。了解你的资源使用情况**
**7。从头开始构建安全性**:要构建安全的服务，有必要在服务设计的一开始就集成安全性
**8。加密是一等公民**:加密必须是您业务的重中之重，因为它是您客户业务的重中之重。
**9。网络的重要性**
**10。没有看门人****

我喜欢这篇文章中的一句话:

> “没有经验的压缩算法。”

点击阅读[全文。](http://bit.ly/10LessonsAWS)

*9 分钟读完*

* * *

### [Java 大规模调试:当罕见事件变得司空见惯](http://bit.ly/DebuggingJava)

通过这个用吉拉云调试间歇性问题的故事，阐述了在大型复杂软件系统中解决问题的本质。

**这篇文章的亮点:**

*   解决问题可能是一个非常反复的过程，提出问题，问题的答案会引出新的问题，这是这个过程的一部分。

*   调试某些东西将需要重复生产中的日志记录。对于大而复杂的系统，人们不能总是对系统的行为进行推理。有时，您需要添加代码来确定生产中发生了什么。

*   调试幸福之路，找到最初的症状，但记住这样做只是漫长旅程的第一步。

*   死胡同也是信息。他们让你回溯并重新检查你所知道的。

[阅读整个故事](http://bit.ly/DebuggingJava)了解深入挖掘问题的逐步过程，从而有效地进行调试。

*9 分钟读完*

* * *

### [缺乏经验的开发人员最容易犯的错误](http://bit.ly/ProgrammerPitfalls)

这是所有开发人员都会犯的一些常见错误。虽然是为初学者写的，但我认为这篇文章对所有开发者都有一些很好的提醒。意识到这些错误并知道如何避免它们可以帮助他们职业生涯中的每个开发人员。

*   重新实现 API 中已经可用的代码:细节决定成败。在开始编写任何代码之前，请务必阅读文档。

*   使事情变得不必要的复杂:不要做奇怪的一行程序和过于复杂的抽象。让代码简单，而不是复杂。

*   **默默吞咽错误**:追查问题源头，不要只是“修复”bug。

*   **过度自信**:如果你不知道自己不知道什么，你就不知道自己错过了多少。

*   切换工具:做研究，挑选你的工具，坚持使用它们&掌握它们。

*   **仅测试快乐路径场景**:为边缘情况编写测试。

*   **专注于技术而非业务**:永远知道你的工作如何影响业务。

点击查看[的完整帖子](http://bit.ly/ProgrammerPitfalls)

*5 分钟读取*

* * *

### [关于什么时候不做微服务](http://bit.ly/dontdomicroservices)

所有企业都必须向客户提供价值。但是当你刚刚起步的时候，你不知道什么会带来价值。你认为会带来价值的一切都只是假设。**对“何时不做微服务”的回答在于了解你正在从事业务“价值交付”生命周期的哪一部分。**一个将技术团队描述为开拓者、定居者的类比&城镇规划者帮助我们理解这一点。

先驱们用狂野的、不同的方法进行实验，进行许多实验，希望减少 3 年多后什么能给公司带来价值的不确定性。这种“开拓性”的努力带来了几个选择，可以在此基础上更上一层楼。“定居者”最终建立了这些选项。他们弄清楚如何扩展产品工程，并致力于在组织中构建辅助部分，以使产品成功地提供差异化价值。随着时间的推移，这些曾经的新产品将不再与众不同，但仍将带来巨大的价值。城镇规划者是从事这些产品的人。

如果你是先行者，坚持独石几乎总是正确的选择。你必须运行许多小实验，建立许多 MVP，然后扔掉许多 MVP。在这种情况下，整体式总是更快的选择。当你是定居者的时候，一旦你发现哪些实验可能会产生价值，并且实际上产生了一些价值，就考虑使用微服务。如果您是城镇规划者，微服务可以帮助您优化速度，是一个不错的选择。

如果你正在考虑你的建筑选择，你必须[阅读](http://bit.ly/dontdomicroservices)[克里斯蒂安·波斯塔](http://bit.ly/ChristianPosta)的这篇文章。

*5 分钟读取*

* * *

页（page 的缩写）这是我在 Appsmith 的团队一周以来阅读的最好的科技文章列表。我每周六都在这里分享。希望听到您对此的反馈。

如果你想了解我或者从我这里开始 **[一个 AMA](http://bit.ly/amadevto)一个 T3。**