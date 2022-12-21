# 使用 Azure 部署插槽减少停机时间

> 原文：<https://dev.to/andreasjakof/reducing-donwtime-using-azure-deployment-slots-2h12>

免责声明:
我们有一个(大部分)纯粹的微软环境。有许多虚拟的 Windows 服务器，上面有 IIS，为我们的内部网页面提供服务。我不知道，其他服务器/ide 是否有类似 WebDeploy 的东西。

有几种方法可以部署到 IIS。

*   WebDeploy [如果你从未使用过它，请阅读这篇文章！](https://blogs.msdn.microsoft.com/ericparvin/2018/05/31/install-and-configure-web-deploy-on-iis/)
*   文件传送协议
*   复制和粘贴

它们都很简单，而且都有相同的缺点。当您进行部署时，您的站点会停机。当部署一个网站时，这可能没问题，但是当你有一个大量使用的 API 时，你希望将停机时间降到最低。最好是零，因为你永远不知道，下一个请求什么时候会到来。如果时间安排不当，你部署应用程序的时间将精确到 500 毫秒。

除了在前面有一个负载平衡器(或类似的东西)和至少两个 IIS 实例之外，我不知道有什么简单的方法可以绕过它。但是，有了 Azure，你就有了所谓的“部署插槽”,它们会派上用场。

您部署到一个部署插槽，并且(如果已配置)发生自动交换。你的应用程序开始运行，如果成功，所有对你的站点/API 的新请求都会被重定向到你的新副本。一旦对旧应用程序的所有请求都得到满足，旧应用程序就会被关闭，而您的新应用程序会单独响应请求。

如何使用部署插槽？
[配置一个](https://docs.microsoft.com/en-us/azure/app-service/deploy-staging-slots)插槽，给你的站点。
[从你的 CD-Pipeline](https://blog.elmah.io/continuous-deployment-to-azure-in-visual-studio-team-service/) 或任何其他方式部署到它。

如果需要，也可以配置自动交换...完成了。