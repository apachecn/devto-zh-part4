# 使用 Scout APM 了解 Heroku 错误代码

> 原文：<https://dev.to/scoutapm/understanding-heroku-error-codes-with-scout-apm-306c>

*这篇文章最初出现在[童子军博客](https://scoutapm.com/blog/understanding-heroku-error-codes-with-scout-apm)上。*

如果你用 Heroku 托管你的应用程序，并且发现你自己在你的实时系统中面临一个无法解释的错误。接下来你会怎么做？也许您没有专门的 DevOps 团队，那么您将从哪里开始调查呢？当然是用侦察兵 APM！我们将向您展示如何使用 Scout 来找出应用程序代码中的问题所在。我们将遍历两个最常见的 Heroku 错误代码，并向您展示如何使用 Scout 快速有效地诊断问题。

## Heroku 的错误记录系统

首先让我们来看看 Heroku 用户是如何出现典型错误的，然后我们来看看如何使用 Scout 来调试这个问题。Heroku 中发生的任何错误都会导致向用户显示一个**应用程序错误页面**,并显示一个 HTTP 503 状态代码，指示“服务不可用”。如果你是一个经常使用 Heroku 的用户，你可能会多次看到这个页面。

[![](img/7d3f50fbb78611513a663001a90e54a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--80-Z16fL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ytjt26tsgqngfi1kk5tq.png)

不幸的是，作为开发人员，这并没有给我们太多的线索。但是我们可以通过深入研究 Heroku 的日志系统来发现错误的细节。我们可以在 **Heroku 仪表板**的**活动**选项卡上查看这些日志，或者使用 Heroku CLI 应用程序:

```
$ heroku logs --tail 
```

Enter fullscreen mode Exit fullscreen mode

Heroku 在其错误代码中使用字母 **H** 、 **R** 和 **L** 来分别区分 **HTTP** 错误、**运行时**错误和**日志**错误。Heroku 报告的所有不同类型的错误的完整列表可以在[这里](https://devcenter.heroku.com/articles/error-codes)找到。从下面的截图可以看出，Heroku 在这种情况下显示的错误是 H12 错误。

[![](img/9cecb2110174ed9c66fc831ca75ec8bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fJjm7Fpq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dndiodwf9vpylqmtcj1e.png)

## 从错误代码到解决方案，用 Scout

所以现在我们已经检查了日志并且发现了发生了什么类型的错误，我们可以对照 Heroku 的文档来修复它，对吗？不完全是。你看，这些错误描述有点模糊，你不觉得吗？他们清楚地告诉我们发生了什么，但他们并没有真正告诉我们为什么会发生。这是 Scout 这样的应用程序性能监控(APM)工具的工作。当你需要知道原因和地点时，Scout 应该是你的首选工具。

幸运的是，Heroku 日志确实告诉我们错误发生的时间以及发生的错误类型，所以现在让我们进入 Scout APM，使用两个常见的示例场景来调查在错误发生时发生了什么。

### 例 1 - H12:请求超时

当一个请求需要超过 30 秒才能完成时，Heroku 抛出这个错误。在 Scout 中，任何需要超过 30 秒才能完成的请求都应该在主概览页面上非常清楚地显示为图表中的一个尖峰，因为这种类型的响应时间将与您通常的流量显著不同。所以那是你调查的第一个地方。

[![](img/97a369b9619d4a5520319facd00600f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rqn59cwz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qsvv7qyp769g6s14i7ty.png)

一旦您在图表上找到了相关的尖峰，在它周围拖放一个框，在该尖峰内发生的端点的便利列表将呈现给您。选择您认为是罪魁祸首的端点，然后选择在错误发生的**同一时间**出现的那个端点上的轨迹。

在这一点上，我们可以检查跟踪，并查看在此跟踪期间时间或内存消耗的细分，按层组织。例如，我们可能会看到一个数据库查询花费了很大一部分时间，该查询源自一个或我们的模型中的一行代码，如果是这种情况，那么我们可以看到该查询和该行应用程序代码的回溯。

但是在下面这个具体的例子中，我们可以看到很大一部分时间实际上花在了文章控制器的编辑动作上。事实上，我们可以看到，处理这个控制器动作用了 40 秒，这就是 Heroku 超时并出现 H12 错误代码的原因。

[![](img/cccf794640d3c9f809580a408b369076.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ASt5zUHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/14ta5xqf6fc8f83m0tbi.png)

我们可以通过启用自动仪器特性来进一步调试这个特殊的例子[，这进一步**分解未测试的控制器时间**，逐行进行。现在我们可以在控制器中看到一行精确的代码，以及显示问题所在的回溯。这里我们可以看到这个应用程序运行了 40 秒的原因是因为在编辑操作的第 22 行调用了 sleep(40)。现在，这一行代码显然只是为了演示的目的而放在那里，但是它给你一个概念，当给你很少的信息时，你能得到的细节的水平。](https://github.com/scoutapp/scout_apm_ruby/pull/247)

[![](img/0cf26d3bdfa48f9fdad2e8360a73e432.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9gUb6PoL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/etnxbtjq4xjk1nmn1ing.png)

#### 其他思路为 H12 错误

*   您是否曾经遇到过这样的情况:有人告诉您，他们在一个端点上遇到了超时错误，但是在您自己访问该端点之后，一切看起来都很好？也许您会在一天中的特定时间或仅从特定用户处断断续续地看到此错误消息，但是无论您做什么，您自己都无法重现该问题。也许 Scout 的**按上下文追溯**可以帮你在这里诊断。
*   “跟踪索引”页允许您按不同的标准查看跟踪，甚至可以按您自己定义的自定义上下文标准查看跟踪。例如，您可以查看与特定用户相关联的所有跟踪，甚至可以查看大型定价计划中的所有用户。一旦您找到了有问题的跟踪，您可以按照我们前面描述的类似步骤来找到根本原因。

### 示例 2 - R14:超出内存配额

您可能会遇到的最常见的 Heroku 错误代码是 **R14“内存配额超出”**错误。当你的应用程序超出了服务器分配的内存量时，这个错误(和它的老兄弟 **R15“内存配额大大超过”**)就会发生。你的服务器分配的内存量取决于你的 Heroku 计划。

[![](img/732d162742c61f0a926d130c1761d3ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iTqXL-bL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htpvlhag4e804l2jfuaw.png)

当您的应用程序耗尽内存并出现 R14 错误时，服务器将开始分页以交换空间，您将开始看到整个应用程序的性能下降。如果它继续增加并达到您配额的 2 倍，将会出现 R15，您的服务器将会关闭。

这是一个很好的例子，说明您可能会遇到错误消息，但是如果没有 APM 工具就无法进行调试。但是不要害怕，因为与竞争对手相比，Scout 在分析内存膨胀方面确实表现出色。

首先，您可以在 Scout 的主概览页面上查找内存异常，您可以选择在图表上以迷你图的形式显示内存。在下面的例子中，您可以看到内存使用在某个时间点是如何快速上升的。此外，我们的**内存膨胀洞察**功能(靠近截图底部)将 ArticlesController#index 确定为一个需要调查的潜在问题。

[![](img/e5c7feb04f3ff9310a2e3c6201f53e58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yb5M5Azm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2gemvpnuwvf3d5ege0pd.png)

当我们查看此时发生的跟踪时(如下所示)，我们可以清楚地看到哪些层正在使用所有这些内存(**视图**层)。我们还可以从回溯中确定原因是没有对查询进行过滤或分页，因此所有记录都被加载到一个页面上。

[![](img/ab03c32810bc5f811b472a25bbe90d49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pFLVoAjF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/327feigfp6lbjz1b1m0v.png)

#### 其他想法为 R14 错误

*   难以追踪的内存问题的一个很好的例子是当只有大用户产生 R14 错误时。在这种情况下，在“跟踪”页面上按上下文查看将有助于您找到这个问题。
*   查看 Web 端点索引页面，您可以按最大分配进行筛选，以查找使用大量内存的端点。这为您提供了一种不同的方法来查找要调查的端点，而不是使用主概览页面的时间线。
*   如果您想了解更多关于内存膨胀的原因，以及如何使用 Scout 解决内存膨胀问题的信息，[请看一下这篇文章](https://book.scoutapm.com/memory-bloat.html)。

## 接下来是什么？

你在使用 Heroku 的时候是否遇到了一个不同的 Heroku 错误，我们在这里没有提到？今天就通过[支持](//mailto:support@scoutapm.com)联系我们，我们将向您展示如何调试 Scout APM 的问题！

Heroku 是一个托管你的应用程序的极好的平台，这就是它在开发者中如此受欢迎的原因。但是也可以清楚地看到 APM 工具对于调试出现的关键生产问题和整体应用程序性能健康监控是多么有用。所以，如果你现在还没有使用 Scout，今天一定要注册免费试用！