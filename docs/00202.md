# 无服务器 Azure 基础

> 原文：<https://dev.to/azure/serverless-azure-fundamentals-pcj>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions/](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=devto-blog-jeliknes)了解更多关于微软 Azure 如何实现你的无服务器功能。

本系列的前几集回顾了如何将现有的 AWS Lambda JavaScript Node.js 函数迁移到 Azure 函数。这一集回顾了一般概念。

# Azure 无服务器基础

这一集回顾了几个概念，包括:

*   使用*资源组*组织相关资产
*   使用 *Azure 资源管理(ARM)模板*和单击*部署到 Azure* 功能来管理基础设施
*   使用 *Azure 仪表盘*定制您的一览式体验
*   深入查看资源的配置设置
*   使用基于网络的*存储浏览器*浏览数据

点击此处观看这一集:

[https://www.youtube.com/embed/HD3iwH1Q64s](https://www.youtube.com/embed/HD3iwH1Q64s)

您可以查看示例应用程序的源代码，并在“AWS migration”GitHub 存储库中单击一下，将迁移的代码直接部署到 Azure。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [耶路撒冷](https://github.com/JeremyLikness) / [移民](https://github.com/JeremyLikness/AWSMigration)

### 从 AWS Lambda 迁移到 Azure 函数

<article class="markdown-body entry-content container-lg" itemprop="text">

# 将 AWS Lambda 移至 Azure 函数

这是“从 Lambda 迁移到 Azure 函数”视频系列的源代码，演示了如何从 AWS Lambda 迁移到 Azure 函数。

<g-emoji class="g-emoji" alias="cinema" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3a6.png">🎦</g-emoji> [观看视频系列](https://www.youtube.com/playlist?list=PL1VfiVM16kp8U5E7U2tfJdskXJg8DPPKL) (YouTube 播放列表)

## 快速启动

[![Free Azure Account](img/d9a3cf269ef7f44a874d4d23a7716180.png)](https://jlik.me/gmj) 获得你的[免费 Azure 账户](https://jlik.me/gmj)

您可以快速开始使用迁移的功能。只需点击“部署到 Azure”按钮。请确保输入唯一的前缀(例如，使用您的姓名首字母或添加一个序列)。部署完成后，您可以访问和测试该功能。

[![Deploy to Azure](img/c520bd44b91f9a1d115e1745f2ff9dea.png)](https://azuredeploy.net/)

> 要启用缓存，请在创建后导航到存储帐户。点击`Table service`下的`Tables`，然后添加一个名为`primes`的表格。

## 代码

这个存储库包含所有相关项目的代码。

### 源(“纯”)函数

函数本身决定了传递的数是否是质数。纯功能在`src\isItAPrime.js`中可用…

</article>

[View on GitHub](https://github.com/JeremyLikness/AWSMigration)

在下一篇文章中，我们将看看在部署到 Azure 之前，如何在本地机器上开发和测试 Azure 功能。

## 资源

1.  [创建您的免费 Azure 帐户](https://azure.com/free?WT.mc_id=devto-blog-jeliknes)
2.  [Azure 门户概述](https://docs.microsoft.com/azure/azure-portal/azure-portal-overview?WT.mc_id=devto-blog-jeliknes)
3.  [ARM 模板](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview?WT.mc_id=devto-blog-jeliknes)
4.  [Azure 基础(免费动手课程)](https://docs.microsoft.com/learn/paths/azure-fundamentals/?WT.mc_id=devto-blog-jeliknes)