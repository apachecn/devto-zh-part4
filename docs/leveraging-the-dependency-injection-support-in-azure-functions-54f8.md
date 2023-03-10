# 利用 Azure 函数中的依赖注入支持

> 原文：<https://dev.to/azure/leveraging-the-dependency-injection-support-in-azure-functions-54f8>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=servsept_devto-blog-cxa)了解更多关于微软 Azure 如何实现你的无服务器功能。

对于许多开发人员来说，诸如依赖注入和控制反转这样的技术在他们的代码库中已经很常见了。尤其是在。NET 空间中，有大量的开源框架和库使得合并这些实践更加容易。

随着软件行业继续稳步采用更多的无服务器技术，开发团队继续寻找方法来利用他们现有的投资进入这个新的无服务器世界。他们希望能够创建模块化组件，并动态交换其实现，而无需重写或部署代码。他们希望能够为他们的代码编写集中的单元测试，并创建抽象出外部依赖的模拟实现。

这些只是将依赖注入支持添加到无服务器函数定位中的一些原因。NET Core in[Azure Functions](https://azure.microsoft.com/en-us/services/functions?WT.mc_id=ondotnet-devto-cephilli)v2。

我们对这个功能非常兴奋，我们决定邀请 Azure Functions 团队的 Fabio Cavalcante 来参加 T2。NET show 向我们展示了它是如何工作的。

[https://www.youtube.com/embed/LTPbaNzJd18?start=39](https://www.youtube.com/embed/LTPbaNzJd18?start=39)

如果你喜欢这个视频，并希望看到更多我们的。净内容，请订阅我们的[。NET 开发者 YouTube 频道](https://www.youtube.com/channel/UCvtT19MZW8dq5Wwfu6B0oxw)。

## 有用的链接

*   在中使用依赖注入。NET Azure 函数
*   [节目中的代码演示](https://github.com/fabiocav/azfunc-di-demo)
*   [利用 Azure 功能提升无服务器应用的开发者体验](https://azure.microsoft.com/en-us/blog/advancing-the-developer-experience-for-serverless-apps-with-azure-functions/?WT.mc_id=ondotnet-devto-cephilli)
*   [使用 Visual Studio 代码创建您的第一个函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?WT.mc_id=ondotnet-devto-cephilli)
*   开始使用。网络