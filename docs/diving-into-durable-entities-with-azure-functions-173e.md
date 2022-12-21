# 用 Azure 函数深入持久的实体

> 原文：<https://dev.to/azure/diving-into-durable-entities-with-azure-functions-173e>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=servsept_devto-blog-cxa)了解更多关于微软 Azure 如何实现你的无服务器功能。

我对无服务器的概念已经着迷了一段时间。能够在不太关心基础设施的情况下构建应用、基于负载的动态扩展和灵活的定价模型的想法完全为我们开辟了一种交付基于软件的解决方案的新途径。

有趣的是，无服务器也有它自己的一套限制，你必须绕过它。您可能经常听说的一个约束是，您应该编写无状态的无服务器代码。但是为什么呢？？

在一个无服务器的世界中，您的应用程序会根据您所使用的平台进行动态配置和销毁。这使得依赖像本地内存、本地变量甚至本地磁盘存储这样的资源几乎是不可能的。当然，您总是可以选择使用数据库或第三方缓存服务，但这可能会使您必须编写的代码变得复杂。

有些问题可能需要高度并发地访问应用程序的当前状态。这使得开发人员不得不小心管理如何读写状态。但是如果你不需要呢？

Azure Functions 团队为他们的平台引入了一个名为[持久功能](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview?WT.mc_id=servsept_devto-blog-cephilli)的扩展。持久函数允许您在无服务器环境中编写有状态函数。

在最新的测试版中，该团队增加了对一个名为[持久实体](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-entities?WT.mc_id=servsept_devto-blog-cephilli)的新概念的支持，这让你可以使用类似于演员模型风格应用程序中的模式。

在这一集里，[克里斯·吉勒姆](https://twitter.com/cgillum)和[塞巴斯蒂安·伯克哈特](https://twitter.com/sebburckhardt)与[杰瑞米](https://twitter.com/JeremyLikness)坐下来，给我们简单介绍一下耐用实体。他们讨论了与 actor 模型、通过 HTTP 调用实体等的区别。

[https://www.youtube.com/embed/faKZXkmJ44g?start=38](https://www.youtube.com/embed/faKZXkmJ44g?start=38)

如果你喜欢这个视频，并希望看到更多我们的。净内容，请订阅我们的[。NET 开发者 YouTube 频道](https://www.youtube.com/channel/UCvtT19MZW8dq5Wwfu6B0oxw)。

## 有用的链接

*   [什么是持久功能](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview?WT.mc_id=servsept_devto-blog-cephilli)
*   [GitHub 上的持久功能](https://github.com/Azure/azure-functions-durable-extension/)
*   [为无服务器状态引入持久实体](https://dev.toIntroducing%20Durable%20Entities%20for%20Serverless%20State)