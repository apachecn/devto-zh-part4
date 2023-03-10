# 什么是无服务器应用程序开发？

> 原文：<https://dev.to/decipherzonesoft/what-is-serverless-application-development-445>

什么是无服务器 Web 应用开发？无服务器 web 应用程序开发是一种新兴的趋势，正在获得极大的欢迎。术语“无服务器”本身听起来令人困惑，因为没有服务器就不可能运行 web 应用程序，那么它实际上是什么意思呢？虽然这个令人困惑的术语的使用似乎是一种营销伎俩，从不同的角度来激发好奇的头脑，但它是一个非常清晰有意义的术语。

***什么是无服务器架构？*T3】**

无服务器架构意味着使用计算运行时，也称为功能即服务(FaaS)平台，它执行应用程序逻辑，但不存储数据。该模型提供了一个完全不同的经济模型“随用随付”,而不是租用服务器和设置服务器实例来部署代码的传统方式。

***全文来源:[https://www . decipherzone . com/blog-detail/What-is-server less-web-application-development](https://www.decipherzone.com/blog-detail/What-is-serverless-web-application-development)*T5】**

无服务器架构是无服务器计算的基础，无服务器计算是一种云计算执行模型，其中云提供商运行服务器并动态管理机器资源的分配。定价基于应用程序消耗的实际资源量，而不是预先购买的硬件容量单位。

阅读:[什么是 API，它是如何工作的？](https://www.decipherzone.com/blog-detail/What-is-an-API-and-How-it-works-)

无服务器计算可以简化将代码部署到产品中的过程。缩放、容量规划和维护操作可能对开发者或运营商是隐藏的。无服务器代码可以与传统风格(如微服务)中先前部署的代码结合使用。

***为什么是无服务器架构？*T3】**

无服务器架构提供了“现收现付”模式，非常适合只需向 IOT 设备发送简单响应或上传图像或文档的代码。

功能即服务(FaaS)平台在运行时提供机器资源的动态可扩展性。

在运行时功能即服务中，当客户端机器请求响应时，平台会在云上找到空实例来运行代码。因此，这种架构有很高的延迟，所以不应该用于整个 web 应用程序。

无服务器架构最适合托管需要高计算能力的功能，如 OCR 引擎、K-MAP 解算器和线性编程解算器。

换句话说，即使有十亿用户同时请求代码，您也需要响应或解决方案的那部分代码，那么您必须将该代码与运行在单独的服务器实例上的其余代码一起托管在无服务器架构中。

***什么是 AWS Lambda？*T3】**

在撰写本文时，具有最大功能可用性的 AWS Lambda 是作为服务平台的最佳功能。AWS Lambda 有巨大的竞争对手 Google Cloud Functions、Microsoft Azure Functions、IBM Cloud Functions 的 OpenWhisk、Project Riff 和 Oracle Fn Project。

阅读:[顶级 Web 开发技术和框架](https://www.decipherzone.com/blog-detail/Top-Web-Development-Technologies-and-Frameworks)

在幕后，每个 AWS 服务调用 AWS Lambda web 服务 API 来服务运行不同 AWS 服务的不同服务器实例，如 S3 bucket、DynamoDB、EC2 实例。现在，AWS 已经允许用户访问这个 API，这允许他们在较低的级别上执行任务，如托管 API 的代码，甚至无需配置新的服务器实例。API 代码在运行时会搜索并声明空的服务器实例来托管 API。

阅读:[为什么渐进式 Web 应用程序开发是趋势？](https://www.decipherzone.com/blog-detail/Why-Progressive-Web-Application-Development-Is-Trending-)

AWS Lambda 可以通过其 web 应用程序控制台和代码进行控制，管理员可以管理用户的访问权限。AWS Lambda 函数可以通过以下 4 种方式调用:

[![Serverless](img/b95a67b986c82eb32d6f3c3a6fa4935e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JRDcqgjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ares.decipherzone.com/blog-manager/uploads/ckeditor_aws%2520gateway.png)

点击这里阅读全文:[https://www . decipherzone . com/blog-detail/What-is-server less-web-application-development](https://www.decipherzone.com/blog-detail/What-is-serverless-web-application-development)