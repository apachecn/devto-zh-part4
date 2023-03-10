# 如何使用亚马逊 S3 和 CloudFront 在 AWS 上托管 React 应用程序

> 原文：<https://dev.to/dschep/how-to-host-a-react-app-on-aws-using-amazon-s3-and-cloudfront-bgo>

这里有一个教程，教你如何部署快速、全球化、廉价的 React 应用程序。

这样做的结果是一个带有 React 应用程序、自定义域和 SSL 证书的网站— **基本上，您需要的一切都已准备就绪。**

通过无服务器框架及其新的无服务器组件功能，所有这些都将变得简单。

这篇文章很少有图片，因为可以在这里找到本教程的完整视频:

[https://www.youtube.com/embed/ts26BVuX3j0](https://www.youtube.com/embed/ts26BVuX3j0)

### 概述

您的 React 应用程序将通过全球内容交付网络分发。这使它能够在世界各地拥有令人难以置信的高速性能。

我们将使用可能最便宜的基础设施来完成这项工作，这就是亚马逊网络服务的 S3 和 Cloudfront。

那有多便宜？

一年之内，你可以传输 50 GB 的数据，每月服务 200 万次请求，**免费**。

之后，根据你传输的数据量，每 GB 将花费你 8-2 美分，每 10，000 个 HTTPs 请求加 1 美分。查看 AWS Cloudfront 定价页面了解更多详细信息。

你必须为你的自定义域名付费。但是，您将从 AWS 证书管理器获得一个免费的公共 SSL 证书。

现在是一个免责声明——在此之前，我没有分析过市场上静态网站托管的所有选择，所以我不知道可能存在更便宜的替代方案。但是，S3 和 Cloudfront 已经存在很多年了。它们既可靠又快速。事实上，许多静态网站托管产品都是建立在 S3 和 Cloudfront 之上的，并收取一定的费用。

开发人员不经常使用普通的 AWS S3 和 AWS Cloudfront 的原因是，与其他产品相比，使用它们的体验太复杂了。

然而，这正是无服务器框架的用武之地。它为所有基础设施提供商带来了出色的开发人员体验，尤其是具有自动扩展、按使用付费，也就是“无服务器”特性的基础设施。

### 调教

在做任何事情之前，你需要有一个 AWS 帐户。你必须有一张信用卡才能注册，尽管在你超过免费等级限制之前不会向你收费。

接下来，登录 AWS，导航到 IAM 仪表板以创建访问密钥，无服务器框架将使用这些访问密钥来提供 React 网站所需的基础设施。

转到“用户”，然后单击“添加用户”。给它一个包含无服务器框架的名称，然后单击“启用编程访问”，并单击“下一步”。

选择“直接附加现有策略”。选中“AdministratorAccess”旁边的框。再次点击“下一步”和“下一步”跳过标签屏幕，然后点击“创建”。

注意:稍后，您可能想要缩小这些权限的范围，这样无服务器框架将具有最少的访问权限。但是，这可能很复杂，因为无服务器框架使用许多 AWS 服务。为了给你提供帮助，网站组件目前使用 S3、证书管理器、Cloudfront 和 Route53，并且需要访问这些组件。

复制“访问密钥 ID”和“秘密访问密钥”。你很快就会需要它们...

现在，该是有趣的无服务器框架部分了。

确保你已经安装了 [Node.js](https://nodejs.org/en/download/) 。然后，通过 NPM 安装无服务器框架并添加全局标志`-g`。

```
$ npm i -g serverless 
```

Enter fullscreen mode Exit fullscreen mode

确保您安装了无服务器框架版本 1.49 或更高版本。提示:如果你正在安装它，你会的。

我们将使用一个网站模板，其中包含您快速入门所需的所有脚手架。这完全是一个样板。使用此命令安装网站模板。

```
$ serverless create --template-url https://github.com/serverless/components/tree/master/templates/website 
```

Enter fullscreen mode Exit fullscreen mode

这将为您创建一个`website`文件夹。

进入该文件夹，运行`npm i`，通过 npm 安装 React 依赖项。

```
$ npm i 
```

Enter fullscreen mode Exit fullscreen mode

同样在`website`文件夹中，创建一个名为`.env`的文件并粘贴你的 AWS 密钥，格式如下:

```
AWS_ACCESS_KEY_ID=A92JA098J10FAJ10JAAFASF
AWS_SECRET_ACCESS_KEY=fJajajaf0919jIJFJA7813hAAFJHL 
```

Enter fullscreen mode Exit fullscreen mode

您已经完成了设置。是时候开始开发了！

### 发展

这个项目使用 React 和 package 来构建和捆绑应用程序。

您也可以使用以下命令在本地使用 Parcel 运行应用程序:

```
$ npm run start 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以快速发展。

### 部署

开箱即用，网站模板应该准备好部署。

如果你查看你的`serverless.yml`文件，你会看到一个名为“网站”的无服务器组件。无服务器组件只是知道如何部署创建特定结果或用例所需的云基础设施的代码。

无服务器组件是开源的，您可以使用它们在无服务器云基础设施上轻松部署许多用例。[点击这里查看](https://www.github.com/serverless/components)。

每个无服务器组件都有一个`inputs`属性，允许您配置组件将提供的用例。例如，这里您可以看到`src`输入，它指向包含您的网站资产的目录。以及一个`hook`，以便在部署前建立您的网站。这样，您不必在部署之前运行构建脚本，它会自动发生。

好了，让我们开始部署吧！

要部署 React 应用程序，只需在网站文件夹中运行`serverless`命令。

```
$ serverless 
```

Enter fullscreen mode Exit fullscreen mode

您的网站应该部署在不到一分钟，你应该得到指向您的生活网站的网址！。

第一次部署总是耗时最长，因为创建云基础架构资源可能比更新它们更耗时。

**设置自定义域，Cloudfront & SSL 证书**

要设置您的自定义域、您的 Cloudfront CDN 和 SSL 证书，您需要执行一个手动步骤:您必须登录您的 AWS 帐户并购买您的域。目前，网站组件仅适用于在 AWS Route53 上购买的域名。在接下来的几周内，我们将支持在不同注册商注册的现有域名。

转到 AWS Route53 的仪表板，注册您的域并等待注册完成。这可能需要一个小时。完成后，只需将自定义域添加到网站组件的输入中。

再次运行`serverless`，它将确认新的`input`，并设置您的自定义域，Cloudfront & SSL 证书。

您需要等待您的新域传播到世界各地的 DNS 服务器。这也可能需要一个小时。一旦它可用，你将能够看到它的生活，以及你所做的任何改变。

每次运行`serverless`来部署对网站的更改时，网站组件都会使您在 AWS Cloudfront 中缓存的资产失效。这使您几乎可以立即看到变化。

### 状态管理

目前，无服务器组件没有远程状态管理。这将在一个月内到来。在此之前，请确保将`.serverless`目录推送到 Github，因为它包含关于已经为您的网站部署的云基础设施资源的状态信息。

### 增加一个无服务器的 API 后端，数据库&更多

如果你想更进一步，像这个网站一样轻松地构建一个全栈应用，请查看[全栈应用模板](https://github.com/serverless/components/tree/master/templates/fullstack-application)。这将为您提供交付快速、廉价和全球完整的无服务器应用程序所需的一切。

尽情享受吧！