# 连续交付度量标准第 2 部分:多久部署一次生产？

> 原文：<https://dev.to/gocd/continuous-delivery-metrics-part-2-how-often-do-you-deploy-to-production-1ioi>

这是系列文章的第二篇- [可操作的持续交付指标](https://www.gocd.org/tags/cd-analytics.html)。[在之前的文章](https://www.gocd.org/2018/10/30/measure-continuous-delivery-process/)中，我们概述了为什么指标对您的 CD 流程至关重要，以及我们建议您测量哪些指标。在本帖中，我们将深入探讨第一个指标:部署频率。

## 什么是部署频率

部署频率也称为吞吐量，是对您的团队部署代码的频率的度量。该指标通常表示为百分比，它回答了“我们多久部署一次生产或 CD 管道中的另一个重要点，如暂存环境？”。

[![deployment frequency concept](img/14ca2883d21aea4e20d62a5088f3a92c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JKIZrdxG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/2bc0e73dce53/Image%25202019-08-04%2520at%25209.32.03%2520PM.png)

我们认为生产部署是 CD 管道中的一个重要点，我们正在统计生产部署发生与未发生的次数。在上面的示例中，我们有 8 个实例或机会要部署，只进行了 2 次部署，所以部署频率是 25%。

## 为什么部署频率很重要

持续交付中的“持续”一词意味着高部署频率。拥有高部署频率意味着你有更多的部署，并且给你更多的机会对你的软件进行反馈。更重要的是，更高的部署频率意味着您可以更快地向最终用户和利益相关者交付价值。

根据 devo PS State 报告团队所做的研究[，与效率较低的同行相比，高功能团队拥有更高的部署频率。在您组织的业务和目标的背景下，为您的部署频率设定一个基准并尽可能地增加它是很好的。](https://puppet.com/resources/whitepaper/2016-state-of-devops-report)

然而，部署频率必须与质量相平衡。您不希望通过删除测试来增加部署频率。您希望能够更频繁地交付生产，同时保持甚至提高质量。这就是 CD 的意义所在，也是部署频率指标所要捕捉的内容。

## 举例:如何使用部署频率

如果您正在测量您的管道，并且部署频率较低，您能做些什么？我们将举一个假设的例子，并使用 GoCD，我们的连续交付服务器，带您了解如何识别和处理部署频率问题。在我们的例子中，团队收到了来自企业的抱怨，说他们经常得不到价值。让我们找出原因。

[![example pipelines](img/a6aa420f69b7d328927e008037d3c95b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7JMtb2lb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/fddcd382e827/Image%25202019-08-04%2520at%25209.32.37%2520PM.png)

上图向您展示了 GoCD 价值流图(VSM)中表示的连续交付管道。在这里，GoCD 贯穿价值流，包括一些单元测试，然后并行的集成测试和冒烟测试，最后是回归测试。最后，还有用户验收测试(UAT)和生产部署。

要了解这里发生了什么，我们首先要了解您的部署频率是否令人担忧。在我们的例子中，很容易使用 [GoCD 企业分析插件](https://www.gocd.org/analytics.html)在 GoCD 中查找这个指标:转到 GoCD 的 VSM 视图，选择您关心的 CD 管道部分，并查看部署频率(在 GoCD 中称为吞吐量)。我们可以看到，吞吐量只有 9%，这意味着在部署的机会中，只有 9%达到生产水平。这个数字太低了。

[![deployment frequency in GoCD](img/016287ce2c372c06ea3ff843703a5278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lfkNIf-w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/1bbd2f978ee2/Image%25202019-08-04%2520at%25209.33.06%2520PM.png)

这些也可以在电子表格或其他工具中手动跟踪。如果你记下每次*构建和单元测试*运行时的状态，以及*生产*管道运行时的状态，你会在通往*生产*部分 CD 管道的路上看到更多的故障。在下面的表格中，您可以看到当您跟踪和绘制运行时间时，在相同的五天时间内， *BuildAndUnitTests* 比 *Production* 运行的次数多得多。同样，您会看到部署频率非常低。

[![deployment frequency in excel](img/cbc4e34d0791bbf86724c2d2f9bd202f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v-iKLDBH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/9fe23e07096f/Image%25202019-08-04%2520at%25209.33.33%2520PM.png)

这里的主要信号显然是部署频率低。但是，我们为什么以及应该如何采取行动呢？这可能有很多原因。我们建议检查以下潜在原因:

*   你的建造速度很慢吗？缓慢和缺乏反馈是否导致开发团队避免经常签入？团队是否通过将变更组合成更大的块并导致更长的延迟来做出响应？
*   您从提交到部署的端到端交付时间是否过长？
*   你有经常失败的构建吗？
*   你有古怪的测试吗？如果测试不可靠，考虑了解哪些是最大的问题，并解决根本原因。
*   你是不是[停止生产线](http://gettingtolean.com/toyota-principle-5-build-culture-stopping-fix)来解决其他问题？
*   您有长期存在的特性分支或不经常合并的拉请求吗？。我们经常看到开发团队在他们的分支上努力工作，但是直到变更被部署，业务才会看到价值。如果你的问题是你的工作在长期分支中进行，考虑特性切换和基于主干的开发。

## 总结

在本文中，我们讨论了什么是部署频率，为什么它很重要，以及如何应对低部署频率。正如我们在[的上一篇文章](https://www.gocd.org/2018/10/30/measure-continuous-delivery-process/)中所讨论的，我们推荐测量另外三个重要的指标:交付时间、失败百分比和 MTTR。这些指标与部署频率密切相关，可以帮助您进一步了解低部署频率的根本原因。我们将在以后的文章中讨论这些指标的相互关系。