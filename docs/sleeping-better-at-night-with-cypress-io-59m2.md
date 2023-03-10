# 有了赛普拉斯，晚上睡得更香

> 原文：<https://dev.to/tlakomy/sleeping-better-at-night-with-cypress-io-59m2>

在我们开始之前，我正在开发一种全新的监控无服务器应用的方式🚀。如果您厌倦了在调试生产事件时在 50 个 CloudWatch 选项卡之间切换，请查看我们的。

* * *

声明:此贴较长(？)，我在 JSHeroes 2019 上发表的演讲的更好版本

# 场景

想象一下。

今天是星期五，下午 3 点。阳光明媚，鸟儿歌唱，尽管你喜欢你的工作，但你已经有点想回家了。

有人在你的办公桌旁停下来，他们说:

> 嘿，我刚刚把那个功能合并到 master 里了，很棒吧？不管怎样，我要去学校接我的孩子了，你介意把它给 prod 吗？

你的心漏跳了一拍。而且不是好的方面。当然，你不希望被视为一个冒名顶替者，你同意释放代码。我是说，*会出什么问题*？

突然你变得越来越紧张。这是为什么呢？

幸运的是，科学给出了答案:

<figure>

[![](img/c67252b8d5615d43df688cdf35363242.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UAkIlJQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2d3plj3mzezs4ic9z3u3.png)

<figcaption>This is not entirely accurate</figcaption>

</figure>

读者们，我想问你们一个问题:

你同意在周五发布你的代码吗？ *为什么？* *有何不可？*

# 所有权和余额

所有人都想有安全感，这是我们最基本的需求之一(就在拥有相对快速的 CI 管道之后)。

如果每一个产品发布都可能导致 bug、生产事故、惹恼客户或者(甚至更糟)在任何发布之前花费*长*小时在回归测试阶段，这很可能导致精疲力尽。

(而且不是[这个](https://en.wikipedia.org/wiki/Burnout_3:_Takedown)一个，那是*我的果酱*

作为一名开发人员，我坚信保持健康的工作生活平衡以及对交付给用户的任何东西都有一种主人翁感。

在 OLX 集团，我们建立了每天有数百万用户使用的网站。在分类广告业务中，发生一次生产事故相当于成千上万(至少可以这么说)恼火的客户和大量浪费的金钱和时间。

我自己的家人通过在 OLX.pl 上卖东西赚外快，如果我们搞砸了，他们的生活会受到影响。

# 机器的兴起

在我的职业生涯中，我有机会与许多开发团队一起工作。在大多数团队中，我们试图用

## T2】🤖*自动化测试*🤖

取得了不同程度的成功。

(*如果你不知道什么是自动化测试(或 e2e 测试)，想象一下雇佣一个打字和点击速度非常非常快的人，你告诉他们如何测试你的应用程序*

### 稳定性问题

我们用过的大多数工具都产生了不可靠的结果。有可能重新启动同一个测试套件 5 次，得到 5 个不同的结果。在某些情况下，只有当火星和金星对齐时，测试才算通过。

随机失败/通过测试实际上比根本没有测试要糟糕得多。如果你没有测试，那么至少你**知道**你正在做 yolo 发布。

### 难写，更难调试

自动化测试并不意味着[干燥](https://kentcdodds.com/blog/aha-programming/)。如果一个测试被破坏了，开发人员打开规范文件并调查哪里出了问题应该是*微不足道的*。我们编写的一些测试使用了[小黄瓜](https://cucumber.io/docs/gherkin/)，虽然这个想法很好，但现实是...次优。

调试测试应该像宿醉时吃一整个比萨饼一样容易。不幸的是，大量的 e2e 工具迫使你去通过詹金斯，贾维斯等数千行日志。只是发现有人更改了登录表单中的一个标签。

# 输入 cypress.io

该死的。

<figure>

[![](img/9f03e5048c27a6e6bc6ab12e5ed49ccc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PITPML69--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0q7jqrvjsdpvb025n0fy.png)

<figcaption>This is written in large font on cypress.io homepage, so it's true</figcaption>

</figure>

Cypress 是第二个(仅次于 jQuery)让我立刻爱上的 Web 开发工具。

我仍然记得我们在 OLX 的一个工程经理想要演示这个“新 e2e 测试工具”的会议。我想的是:

> 是的，这将是同样的废话，但不同的 API

然后我看到了这个:

<figure>

[![](img/0d404fd289837bec058f605dedb8b882.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3LaxnHNi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htjck1gii87xp0mt4z71.png)

<figcaption>What e2e testing was meant to be</figcaption>

</figure>

让我快速介绍一下您可以在 cypress UI 中做些什么

*   你可以看到你的测试在现场进行
*   有一份详细的日志告诉你发生了什么
*   你可以在任何时候停止测试，测试的应用程序是完全交互式的，你可以看到你是如何同时登录和退出的
*   点击一个请求后，你可以看到应用程序在请求触发前后的状态。
*   在开发测试时，对规范文件的每一个更改都会导致测试的重新开始。我提到过那个赛跑运动员跑得飞快吗？(*非常快，仅供参考*)
*   测试可以被记录，你可以查看失败测试的截图和视频记录(或者通过测试的，但是我认为这并不有趣)
*   调试？打开谷歌 Chrome 控制台，像往常一样调试。

# 仪表盘

这不仅仅是关于跑步者。在 OLX 集团，我们也是 cypress dashboard 服务的满意客户，该服务使我们能够很好地了解我们的测试状态，并并行运行测试，这为 T2 节省了很多时间。

[![](img/29a77ebd665276267db36547f046571b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cXZ4ibJl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/idbffnmssvik11ks5wqb.png)

鉴于我们在波兰支持欧洲多个国家，您能想象手动点击所有这些内容吗？

<figure>

[![](img/79a2168b78aa6b50228249613adc2da1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bzYTrMTF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/707ty8wxfs68ufhyyb3i.png)

<figcaption>This is not even 40% of the full screenshot</figcaption>

</figure>

# 我为什么要写这个？

自从我们收养了赛普拉斯，我知道我可以把东西合并到一起，然后回家。

考虑到我们编写的测试数量，引入一个会破坏我们产品的代码变化是非常困难的。我们并不完美，我们的目标是不断改进，但我个人设法避免了一些潜在的生产失误...拜赛普拉斯测试所赐。

我晚上能睡得更好，我希望你也一样。

试试吧，我想你会喜欢的——我很想在 Twitter 上听到你的想法。

你知道他们说什么:

> 测试你的代码，而不是你的耐心