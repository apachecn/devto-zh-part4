# 在 UI 中保持乐观

> 原文：<https://dev.to/tiagodcosta/being-optimistic-in-ui-511k>

[![Alt Text](img/273a7278172b07024bca551485b6e971.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WnPo42Nr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cyaubn441xuo32zt04k3.jpg)

乐观的用户界面并不是一个新事物(游戏开发者长期以来一直在使用客户端预测的概念)，但是这个概念最近得到了 T2 的大量关注。

这个想法很简单。基于用户动作，界面被更新，即使对后端的请求可能仍然是未决的。最后，乐观的 UI 只不过是一种管理[感知性能](https://blog.teamtreehouse.com/perceived-performance)和避免加载状态的方法。

工作流在反应上可以是简单明了。

1.  捕捉用户操作(例如，单击按钮)。
2.  更新本地状态，这将更新 UI。
3.  发送请求。
4.  得到回应。
5.  如果是否定的，回滚更新本地状态(步骤 2)。
6.  如果是肯定的，什么也不做或确认步骤 2。

消除 UI 交互中的小延迟似乎可以创建更快、更灵敏的用户体验。然而，乐观的用户界面也有一些缺点。

首先，乐观的 UI 不是一个万能的解决方案。一个关键点是，如果服务器出现故障([假阳性](https://en.wikipedia.org/wiki/False_positives_and_false_negatives))，UI 更新应该被优雅地恢复，如果用户触发的动作在应用程序的路由中起作用，这很难实现。

此外，由于存在误报的风险，乐观的 UI 似乎不是检查航班或现金转移体验的好主意。想象一下，对用户说这个现金转移被恢复了。

另一方面，对于不太重要的行为(例如，像一个帖子或发布一个快速消息)，乐观的 UI 似乎还可以。

第二点是，乐观 UI 与生成布尔值(真或假，是或否)的操作一起工作得更好，在误报的情况下，可以很容易地恢复。

以我的经验来看，当事情不顺利时，这种技巧最关键的一点是让它变得引人注目。处理 UI 错误非常重要。动作和错误信息之间的间隔不能超过两秒[。](https://www.nngroup.com/articles/response-times-3-important-limits/)

我创建了一个小应用程序来展示乐观 UI 是如何实现的。只要您回答了其中一个问题，UI 就会更新，即使请求仍处于待定状态。app 可以看到[这里](https://optimistic-updates.netlify.com/)，代码是[这里](https://github.com/tiagodcosta/optimistic-updates)。

这篇文章是关于 UX 和 UI 工程系列文章的一部分。

布鲁克·卡吉尔在 [Unsplash](https://unsplash.com/@brookecagle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片