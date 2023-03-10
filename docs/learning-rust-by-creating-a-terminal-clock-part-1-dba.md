# 通过创建终端时钟学习 Rust 第 1 部分

> 原文：<https://dev.to/bmitch/learning-rust-by-creating-a-terminal-clock-part-1-dba>

在本系列的上一篇(也是第一篇)文章中，我介绍了我创建的 Rust 项目，它帮助我学习 Rust。这个项目是一个终端时钟。

那时我已经完成了分钟和小时进度条。

今天的最新更新是我增加了日和月进度条。

[![Alt Text](img/580e2a1638c29b817759940cbb06ffec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vNQamLfM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/is1z4sj66tzdxa0rxilm.png)

我的这个项目是基于 https://github.com/rothman857/chronometer 的，它也列出了一个年和世纪进度条，我也可以添加，但我还不能 100%确定。

我制造了一些问题。我特别感兴趣的一点是创建 CI 渠道。将不得不调查什么类型的工具可用于生锈(棉绒，代码质量分析等...)因此，如果任何人有任何想法，请留下评论。

代码也急需清理。我现在正处于“让它工作，不管它有多难看”的模式，所以是时候后退一步，做一些重构，这将使项目更有效地继续下去。

感谢所有的反馈。在回购中随意记录问题或发送拉取请求:[https://github.com/bmitch/relogio](https://github.com/bmitch/relogio)