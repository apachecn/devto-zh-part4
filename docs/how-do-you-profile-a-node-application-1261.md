# 如何分析节点应用程序？

> 原文：<https://dev.to/danialmalik/how-do-you-profile-a-node-application-1261>

我有一个论坛应用程序，在 node 和 express 中实现，与另一个项目集成，并导致一些路由和操作的严重延迟问题。我想找出那些成为这些缓慢操作瓶颈的代码片段，并想做一些分析。有什么工具可以帮我吗？现在我已经编写了一个计算请求总时间的中间件，但是如果我能得到类似堆栈跟踪和请求 URL 的东西，类似 django-silk 为 django 应用程序提供的东西，那就更好了。