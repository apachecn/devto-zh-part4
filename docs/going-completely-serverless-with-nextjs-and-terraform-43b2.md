# 使用 Nextjs 和 Terraform 实现完全无服务器化

> 原文：<https://dev.to/ematipico/going-completely-serverless-with-nextjs-and-terraform-43b2>

如果您在一家拥有无服务器基础设施的公司工作，您可能会对这个小故事感兴趣。

我们上线了一个运行在云基础设施上的电子商务网站。

## 为什么？

搜索引擎优化对我的客户来说很重要，因为它对很多网站都是如此。当我们推出网站的第一个版本( [SPA](https://en.wikipedia.org/wiki/Single-page_application) )时，我们必须将重定向、元数据、标题、描述等放在适当的位置。在我们发布几周后，我们注意到在 google 上新的页面没有被正确的索引。我们相信 googlebot 能够执行 JavaScript 并理解异步代码，但事实并非如此，或者至少它没有像我们预期的那样工作。

这时，我们决定重构现有的应用程序，并使用 [Nextjs](https://nextjs.org/) 。它提供了 SSR，这正是我们想要的。

## 部署

几天后，我开始评估如何在生产中部署新网站。作为老派，我可以设置一个 web 服务器并返回请求的 HTML。尽管这在无服务器上效果不佳，原因有几个。尤其是用 Nextjs。

Nextjs 最近发布了一个奇妙的功能，允许我们导出页面文件，可以直接在[无服务器环境](https://nextjs.org/blog/next-8#serverless-nextjs)中使用！但是，有一个问题:这些文件不能直接扔给你的云提供商！在这里，您没有太多关于如何进行的文档。

那时我和我来自 DevOps 的同事决定使用 Terraform(而不是 CloudFormation)来创建我们的无服务器资源。是的，我的客户使用 AWS 作为云提供商。

我确实探索了不同的解决方案。[无服务器框架](https://serverless.com/)就是其中之一。不幸的是，这与我们拥有的页面数量不相称。经过粗略的估计，我们发现每个页面大约需要 10 个资源，在每次部署中总共需要修改/销毁/创建 300 个资源！这些的成本是巨大的(金钱和时间方面)。

## 整合

地形是我们要走的路。那时我确实开始创建一个脚本来连接 Nextjs 和 Terraform。

正如我前面所说，如何将 Nextjs 无服务器构建与现有云提供商集成的例子并不多。目前有的例子只使用[现在的](https://zeit.co/now)，这是一个属性软件。

在与我的同事并肩工作了几天后，我最终拥有了一个健壮的(不太棒，因为仍有改进的空间)构建脚本，它连接了这些工具，并能够为 Terraform 提供将所有 lambdas 部署到 AWS 所需的资源！

## 开源

我决定开源这个项目，使它成为一个库，可以与 Terraform 支持的所有云一起使用。

[**Terraform Nextjs 插件**](https://github.com/ematipico/terraform-nextjs-plugin)

## 如何使用

只需创建一个文件`build.js`，让我们把这个代码:

```
const generateResources = require('@ematipico/terraform-next-plugin');
const routes = {
  prefix: '',
  mappings: [
    { page: '/contactUs', route: '/contact-us'},
    { page: '/aboutUs', route: '/about-us'},
    { page: '/product', route: '/products/:product', params: {
       hideComments: false // <= it tells Terraform that this is not mandatory as query string param
    }}
  ]
};
generateResources({
  gatewayKey: 'MyAwesomeProject',
  lambdaPath: '../../ui-project',
  provider: 'AWS',
  routes
}, true) // <= write configuration on disk 
```

Enter fullscreen mode Exit fullscreen mode

这个截图将生成两个文件，应该给 Terraform: `gateway.terraform.tf.json`和`lambdas.terraform.tf.json`。

*你应该在运行了`next build`命令*之后运行`build.js`脚本。

它们的名称中有`tf`的原因是因为 Terraform 能够检测、验证和规划它们作为自己的配置。你可以在这里了解更多信息。

当然，您仍然需要一个主 Terraform 配置文件来部署一切。所以还有一些 DevOps 的工作要做。这个库可能包含它，但这取决于消费者的需求。

可以查看一下[集成测试](https://github.com/ematipico/terraform-nextjs-plugin/blob/master/integration/aws/api.tf)中的一个小例子。

该库现在正在不断发展，以确保它涵盖了大部分的需求！