# Dota2 实时比赛查看器

> 原文：<https://dev.to/doubledare704/dota2-live-matches-viewer-1gea>

我已经建立了 web 服务，能够实时跟踪 dota2 职业比赛并观看最近的比赛:

[http://dots ider . com](http://dotainsider.com)

回购是关闭的，但我可以告诉你，它是在最新版本的 aiohttp 上开发的。这个 python 框架是我用异步 I\O 操作编写大小项目的最佳选择。为了存储数据，我使用 MongoDB。前部由 vuejs 制成。

这个项目中最具挑战性的部分是在一个地方收集所有有用的数据，但官方 web API 在分布式 API 端点中有一些字段。

现在它正在开发中，但我认为它是稳定的:)