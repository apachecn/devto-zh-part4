# AWS Amplify 的 12 项新功能

> 原文：<https://dev.to/aws/12-new-features-of-aws-amplify-4i8h>

在过去的几个月里，AWS Amplify 增加了不少新功能。在这篇文章中，我概述了一些最受欢迎的框架，让你快速了解框架增加了什么。

## 1。本地模拟/测试&图形编辑器

新的 [local mocking](https://aws-amplify.github.io/docs/cli-toolchain/usage#mocking-and-testing) 特性使您能够在本地开发、调试、建模和测试您的 AWS AppSync GraphQL APIs。您还可以测试存储、无服务器功能，并使用现有的 GraphQL code-gen 特性在本地生成代码。

随着测试服务器的运行，您的应用程序可以在本地环境下运行，同时您可以对数据进行建模并测试您的 API 和前端集成。GraphiQL 是内置的，因此您也可以直接在浏览器中测试突变、查询和订阅。

测试完成后，您就可以像往常一样，将您的更改部署到云中的可伸缩、高度可用的后端服务。

一段视频胜过千言万语:

[https://www.youtube.com/embed/vC9y0qc5PP4](https://www.youtube.com/embed/vC9y0qc5PP4)

## 2。艾& ML

Amplify 最近增加了[预测](https://aws-amplify.github.io/docs/js/predictions)类别，允许您使用各种托管服务将人工智能& ML 集成到您的应用程序中，这些服务允许您做以下事情:

*   将文本从一种语言翻译成另一种语言
*   将文本转换为语音
*   从图像中识别文本
*   实体识别
*   标记真实世界的对象
*   文本的解释
*   上传图像用于自动训练
*   抄写文本

我写了一篇文章展示如何构建一个支持人工智能的 chrome 插件，你应该看看:

[![dabit3 image](img/7532b2e9c1a86c1b02d84d51693afa57.png)](/dabit3) [## 如何构建人工智能支持的自然语言合成 Chrome 扩展

### Nader Dabit 9 月 2 日 198 分钟读取

#javascript #chromeextension #awsamplify](/dabit3/how-to-build-an-ai-enabled-natural-language-synthesization-chrome-extension-1bhl)

## 3。多重授权支持

[添加了多身份验证支持](https://aws-amplify.github.io/docs/cli-toolchain/graphql#allowed-authorization-mode-vs-provider-combinations)来启用公共/私有控件，您可以混合使用&匹配和多个用于托管 GraphQL APIs 的身份验证提供程序(API Keys、IAM、Cognito 用户池、OIDC)。

这使得由托管 GraphQL 后端支持的大量新应用程序的构建更加容易。

> ![Ricardo profile image](img/ac7b255b2868c3fa14138aab3b5cbdcf.png)李嘉图[@ undef _ obj](https://dev.to/undef_obj)![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)在 Amplify CLI 和 GraphQL Transformer 中通过 AWS AppSync 宣布对您的 API 的多身份验证支持😎aws-amplify.github.io/docs/cli-toolc…23:40pm-2019 年 9 月 11 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1171931676886228992)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1171931676886228992)32[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1171931676886228992)11

## 4。λ函数解析器

[现在可以使用 Amplify CLI 中包含的 GraphQL 转换器添加 AWS Lambda 函数](https://aws-amplify.github.io/docs/cli-toolchain/graphql#function)作为 AWS AppSync API 的数据源。

您还可以通过 Lambda 函数授予与 AWS 资源交互的权限。

如果你有兴趣了解更多，我已经在博客中介绍了如何使用 Lambda resolvers 构建加密货币应用程序:

[![dabit3 image](img/7532b2e9c1a86c1b02d84d51693afa57.png)](/dabit3) [## Lambda 函数图 SQL 解析器

### 纳德达比特 6 月 11 日 197 分钟阅读

#serverless #graphql #javascript](/dabit3/lambda-function-graphql-resolvers-11cd)

## 5。Lambda 函数触发器

现在，您可以直接从 Amplify CLI 设置 AWS Lambda 触发器。

使用 Lambda 触发器，您可以调用基于事件的 Lambda 函数进行身份验证、数据库操作和来自其他 AWS 服务的存储操作，如 Amazon Simple Storage Service(Amazon S3)、Amazon Cognito 和 Amazon DynamoDB。

现在，Amplify CLI 允许您启用和配置这些触发器。CLI 为您提供了触发器模板，您可以根据自己的使用情况自定义这些模板，从而进一步简化了这一过程。

Lambda 触发器非常有用，可以将用户添加到组中进行基于组的授权，在图像或文件上传到 S3 时调用 Lambda 函数，或者在数据库中放置或更新项目时调用函数。

要了解更多信息，请点击查看文档[。](https://aws-amplify.github.io/docs/cli-toolchain/quickstart#adding-a-lambda-trigger)

## 6。GraphQL 转换的自定义索引

**@key** 指令允许您为 **@model** 类型建模和配置自定义索引结构。这为您的数据提供了多种灵活而强大的访问模式。

点击了解更多[。](https://aws-amplify.github.io/docs/cli-toolchain/graphql#key)

## 7。支持亚马逊极光无服务器

现在，在构建移动和 web 应用程序时，您可以使用现有的 Amazon Aurora 无服务器数据库作为 AWS AppSync GraphQL APIs 的数据源。

这使您能够使用 Amplify CLI 生成一个 GraphQL API，该 API 带有一个自动生成的模式和解析器，可用于现有的 Aurora 无服务器数据库。

除了现有的 DynamoDB for NoSQL、Lambda functions、Elasticsearch、HTTP 和 None 之外，这又增加了一个一流的关系数据库数据源。

要了解更多信息，请点击查看博文[。](https://aws.amazon.com/blogs/mobile/aws-amplify-announces-new-amazon-aurora-serverless-and-graphql-transform-features-for-building-aws-appsync-apis/)

## 8。简化的 OAuth 流程

现在可以配置 OAuth 2.0 授权流，并从 Amplify CLI 启用 Amazon Cognito 托管的 UI。以前，您必须到 Amazon Cognito 控制台进行设置，并在 web 或移动应用程序中手动构建适当的应用程序配置。

对于 Amplify XR 类别，Amplify 框架为增强现实(AR)或虚拟现实(VR)场景提供了简化的配置设置，这些场景由 Amazon Sumerian 在您的移动和 web 应用程序中提供支持。该框架现在允许您使用 Amazon Cognito 和 IAM 从 Amplify CLI 自动向场景添加授权。

要了解更多信息，请点击查看博文[。](https://aws.amazon.com/blogs/mobile/amplify-framework-simplifies-configuration-for-oauth-flows-the-hosted-ui-and-ar-vr-scenes-for-mobile-and-web-developers/)

## 9。放大器控制台支持即时缓存失效

Amplify 控制台现在支持即时缓存失效，使您能够即时将更新部署到您的单个页面或静态应用程序，而不会放弃内容交付网络(CDN)缓存的性能优势。

这意味着您不再需要担心设置生存时间(TTL)值或等待缓存失效请求来传播您的更改。您可以“推送”您的代码，并在构建成功后立即看到您的更改。

要了解更多关于 Amplify 控制台缓存失效的工作原理，请点击查看帖子[。](https://aws.amazon.com/blogs/mobile/aws-amplify-console-supports-instant-cache-invalidation-and-delta-deployments/)

## 10。三角洲部署

对于增量部署，只重新部署修改过的文件，这缩短了大型网站的部署时间。

对于拥有超大型站点的用户来说，这一特性大大加快了部署速度。它确保只部署在每次构建期间被修改的资产。下图显示了一些在生成过程中跳过部署的文件。

要了解这是如何工作的更多信息，请查看这里的帖子。

## 11。放大控制台网络挂钩

新的 webhooks 功能使您能够使用第三方应用程序，如 Contentful 和 Zapier，在 Amplify 控制台中触发部署，而无需提交代码。

您可以将 Contentful 等无头 CMS 工具与 Amplify Console incoming webhook 功能配合使用，以便在每次内容更新时触发部署—例如，当博客作者发布新帖子时。

现代 CMS 本质上是无头的，这给了你用任何技术开发的自由，因为内容本身没有表示层。内容创建者获得了将内容的单个实例发布到 web 和移动设备的额外好处。

要了解这是如何工作的更多信息，请查看这里的帖子。

## 12。放大控制台手动部署

Amplify Console 最近推出了一个手动部署选项，让您能够在不连接到 Git 存储库的情况下托管静态 web 应用程序。您可以部署存储在您的桌面、亚马逊 S3 或任何云提供商存储的文件。

在过去，部署的唯一方法是将应用程序从它的 Git 存储库中连接起来。

要了解这是如何工作的更多信息，请查看这个帖子[。](https://aws.amazon.com/blogs/mobile/deploy-files-s3-dropbox-amplify-console/)

# 路线图

我们路线图上的很多东西都在我们的 GitHub 问题中公开讨论过。查看与 Amplify 客户端[这里](https://github.com/aws-amplify/amplify-js/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+rfc)和 Amplify 客户端[这里](https://github.com/aws-amplify/amplify-cli/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+rfc)正在讨论的一些内容。

* * *