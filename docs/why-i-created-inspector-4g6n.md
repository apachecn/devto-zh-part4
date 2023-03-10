# 我明确地放弃了自由职业的承诺，现在我全职工作，让我的 SaaS 产品盈利。

> 原文：<https://dev.to/ilvalerione/why-i-created-inspector-4g6n>

大家好，我是 Valerio，意大利的一名软件工程师和 Laravel 认证开发人员。在意大利和法国，我和团队一起工作了 10 多年，帮助他们改进软件。

在过去的几个月里，我努力工作，开始了我在 SaaS 的新冒险，名为 Inspector——面向 Laravel 开发人员的无痛实时监控仪表板。

我明确地离开了我的自由职业承诺，现在我正全职工作，让 Inspector 成为后端开发人员最简单可靠的监控工具。

作为开发人员，我们的部分挑战是试图预测在产品中发布软件时可能发生的所有问题。遗憾的是，不可能预测到所有的问题。这是开发者生活的事实。

在代理公司或作为自由职业者，我经历了一个更复杂的场景，因为我们需要管理多个项目和客户。我们与客户有着紧密的联系，如果项目进展不尽如人意，我们也会面临很大的竞争风险。

下面的简短调查说明了令人悲伤的现实:

[![](img/24cb5700fcc5c918a5832d008f74204a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tQpugOEf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AZUa7qjr4gw6640EnKsbkmQ.png)

## 大量的时间浪费在检查上。没有新的东西产生。

没有什么比我们的时间更宝贵了。然而，作为开发人员，我们经常花费高达 50%的时间来检查我们的应用程序是否正常工作(可能是因为害怕？).

大量的时间被浪费了，却什么也没有产生。

作为一名软件工程师和 Laravel 认证的开发人员，我花了十多年的时间帮助团队改进他们的软件，我经常经历这种令人沮丧的现实。

当我和一个新客户一起做一个新项目时，我有完全相同的感受。在第一个设计阶段之后，该应用程序将投入生产，并开始为真实用户服务。

我们知道面对问题和缺陷我们无能为力，如果出现问题，我们将不得不尽最大努力管理客户。

但是，我们制造软件；自动化是我们的基因，但这是一个陷阱。我们总是专注于如何自动化客户的工作，而忘记了我们可以用自己的工作做同样的事情(甚至更好)。

采用一个自动化的工具来监控我的软件，并在出现任何错误时提醒我，这对我让客户满意并满怀信心地进行代码重构的能力有着巨大的影响。

这就是为什么我对这个话题充满热情。我写过许多关于实时自动化监控的文章。

## 创建自动化性能检测的软件解决方案

我们都知道“时间就是金钱”这句话

我很清楚，让我们用一半的时间做同样的工作的工具可能是一个真正有价值的解决方案。考虑到这一点，我开始开发一种更好的方法来检查应用程序。

我从我的习惯开始，比如检查我的应用程序以确保一切正常。我广泛研究和开发的结果是督察。

我可以信任 Inspector 全天候为我调试应用程序，并实时提醒我任何问题，以便我可以立即解决它们。

### 检查员与其他解决方案

您可以找到许多其他工具来监控您的应用程序，但是市场上的大多数产品对于网络代理和自由职业者来说都有明显的负面特征:

*   **它们是为基础设施监控** (CPU、RAM、带宽)而设计的，并不特定于应用程序级别。此外，他们是多面手，不会给你提供关于你的平台的具体信息(例如 WordPress、job、artisan command 等的插件和主题性能。).
*   他们要求你在服务器级别安装软件。 Inspector 是第一个有意避免这种情况的解决方案。
*   它们很难集成和使用。您需要花费数周时间来配置您的环境。使用 Inspector，你可以运行一个简单的“composer require”或者安装一个插件。
*   他们不适合团队工作。
*   它们不是为 PHP 设计的。

检查员是专为网络机构和自由职业者。它不需要在服务器级别安装，因此对于那些使用 Cloudways、RunCloud 等托管云托管服务的人来说是完美的。

## 接下来是什么

下一个目标是将感兴趣的开发者吸引到网站上，并收集尽可能多的反馈。我开始制作有价值的技术指南，让其他开发者通过使用 Inspector 获得更好的结果。

收到第一次反馈后，我记下了我将立即着手改进的地方:

*   改进用户界面
*   扩展文档
*   制作更多“逐步”指南

其他开发者的反馈正在推动产品路线图，如果你能加入我的旅程，我会很高兴。

*试试 Inspector，是免费的，把你的想法写给我:*[https://www . Inspector . dev](https://www.inspector.dev)