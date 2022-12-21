# 为 Azure 函数创建自定义绑定

> 原文：<https://dev.to/azure/creating-custom-bindings-for-azure-functions-5fb9>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=servsept_devto-blog-cxa)了解更多关于微软 Azure 如何实现你的无服务器功能。

在我看来， [Azure Functions](https://azure.microsoft.com/en-us/services/functions/?WT.mc_id=devto-dotnet-cephilli) 最非凡的特性之一是它能够声明性地连接到外部资源，并在您的函数代码中使用它，而不需要非常了解底层 API。

这个概念被称为*绑定*，通过它，您可以与各种外部服务进行交互，作为您的无服务函数的输入或输出。当然，绑定是可选的，一个函数可能有一个或多个输入和/或输出绑定。

开箱即用，Azure Functions 提供了多种[支持的绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?WT.mc_id=devto-dotnet-cephilli#supported-bindings)。这些允许你轻松地与服务集成，比如 [Twilio](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-twilio?WT.mc_id=devto-dotnet-cephilli) 、 [Azure Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-service-bus?WT.mc_id=devto-dotnet-cephilli) 、 [SendGrid](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-sendgrid?WT.mc_id=devto-dotnet-cephilli) 、 [Cosmos DB](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-cosmosdb?WT.mc_id=devto-dotnet-cephilli) ，甚至 [HTTP](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook?WT.mc_id=devto-dotnet-cephilli) 。

但是，如果您想创建一个不在受支持绑定列表中的服务绑定，该怎么办呢？这可能是您公司依赖的第三方服务，甚至可能是您创建的内部服务。

这是一个如此普遍的问题，以至于我们决定邀请我们的朋友马蒂亚斯·夸兰塔参加我们的*节目。NET* 向我们展示如何为 Azure 函数创建我们自己的定制绑定。马蒂亚斯还非常友好地为 [GitHub 库](https://github.com/ealsur/functions-extension-101)提供了一步一步的指导，我们任何人都可以跟着做。

[https://www.youtube.com/embed/vKrUn9qiUI8?start=58](https://www.youtube.com/embed/vKrUn9qiUI8?start=58)

如果你喜欢这个视频，并希望看到更多我们的。净内容，请订阅我们的[。NET 开发者 YouTube 频道](https://www.youtube.com/channel/UCvtT19MZW8dq5Wwfu6B0oxw)。

## 有用的链接

*   [Azure 函数触发器和绑定概念](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?WT.mc_id=devto-dotnet-cephilli)
*   [演练和代码示例](https://github.com/ealsur/functions-extension-101)