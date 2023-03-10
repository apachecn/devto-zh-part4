# 如何在没有 VPC 的 AWS 上创建安全的内部 API

> 原文：<https://dev.to/eoinsha/how-to-create-secure-internal-apis-on-aws-without-vpcs-5e08>

假设您在 AWS 中有一个无服务器部署，带有外部的、面向公众的 API 以及这些 API 背后的一些 Lambda 函数。随着部署的增长，您可能需要系统的隔离部分(微服务)之间的内部通信。这可以分为三类:

1.  *发布/订阅式事件驱动沟通。*这是一个服务发布已发生事件的地方。另外，订阅服务根据它们的职责按需对事件做出反应。
2.  *点对点事件驱动通信。*在这里，您可以将队列作为服务的一部分，以便它可以接收待处理的消息。例如，电子邮件服务可能接收包含`to`、`from`、`subject`和`from`字段的消息，并使用这些字段构建和发送使用 [SES](https://aws.amazon.com/ses/) 或 [SendGrid](https://sendgrid.com) 的电子邮件
3.  *点对点同步通信*。这是调用服务需要向另一个服务(可能是内部的或外部的)发出请求并阻塞等待响应的时候。例如，向用户服务发出请求，根据授权头中的用户 ID 查找用户的电子邮件地址。

[![](img/494a616462f0ccff9df324c4d09cc9f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P8imRIWR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lbb187c0at30tifvn5jj.png)

# 服务间的同步调用

本帖讲的是第三种情况；点对点同步通信。在无服务器上下文中，您可以通过函数到函数的调用来实现这一点。在 AWS 中，这将使用[λ. invoke](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/Lambda.html#invoke-property)。调用服务可以授予自己正确的 IAM 权限，以调用由其 ARN 定义的目标 Lambda。但是，这可以看作是[的臭味](https://en.wikipedia.org/wiki/Code_smell)！它*泄露了*关于你正在调用的服务的实现细节。如果您想用其他东西来替换 Lambda 服务，比如外部 web 服务、基于容器的实现，甚至是不需要 Lambda 代码的托管 AWS 服务，那么就有问题了。然后，您必须替换实现，并在每个单独的调用服务中更改代码。

相反，通过将实现放在 HTTP 接口后面，可以实现简单的抽象。HTTP 无处不在，很容易理解，并且可以在您更改底层实现时进行维护，而不会有任何重大变化。对于我们的无服务器、Lambda 支持的实现，这意味着将它们放在一个 [API 网关](https://aws.amazon.com/api-gateway/)之后，就像我们的外部 API 一样。

# 保护内部 API 的选项

接下来的关键问题是:我们如何保护它们？我们只希望我们的内部 API 在内部被访问。在我看来，这些是你的选择:

1.  使用 VPCs
2.  使用 API 密钥来保护 API 网关
3.  在 API 网关上使用 AWS IAM 授权

VPC 方法需要将您的调用 Lambdas 放在 VPC 中，并将 API 网关定义为带有 VPC 端点的私有 API。如果可能的话，不要给你的 Lambdas 使用 VPC！。这将限制您的 Lambda 可伸缩性和额外的(8-10 秒)冷启动时间。[崔琰](https://twitter.com/theburningmonk)在[生产就绪无服务器](https://www.manning.com/livevideo/production-ready-serverless)视频课程中很好地涵盖了这个话题。

**更新 2019/10/17** : *自从这篇文章第一次写出来，AWS 已经修改了 VPC 的 ENI 分配方法，因此 VPC 中的 Lambdas 的冷启动惩罚开始消失。这尚未推广到所有主要地区，但它将显著改变局面。在许多情况下，我仍然会避免 VPC，除非因为管理 VPC 的额外复杂性而有必要。*

API 密钥方法似乎是合理的。您可以为内部 API 服务生成一个 API 网关 API 密钥，并将其共享给调用服务。发出请求时，密钥被添加到授权头中。我已经做到了这一点，它的作品。这里的挑战是共享 API 密钥。为了做到这一点，我必须创建一个定制的 CloudFormation 资源来将 API 密钥存储在 SSM 参数存储中，这样它就可以被其他有权限访问密钥的内部服务发现。API 键旨在控制对带有配额的外部 API 的访问，因此内部 API 并不是它们真正的目的。如果你有兴趣了解如何做到这一点，看看我的解决方案[这里](https://github.com/fourTheorem/slic-starter/blob/8e29ec7e688061f094cddd2357867a63ae470416/user-service/sls-resources.yml)。如有任何问题，请在下方评论或[发微博给我](https://twitter.com/eoins)。

# 带 IAM 的内部 API 网关安全

最佳实践建议是在 API 上使用 IAM 授权。如果你使用的是无服务器框架，看起来是这样的:

```
get:
  handler: services/users/get.main
  events:
    - http:
        path: user/{id}
        method: get
        cors: false
        authorizer: aws_iam 
```

Enter fullscreen mode Exit fullscreen mode

这将导致相关联的`ApiGateway::Method` [云信息资源](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/#authorizationType)中的`AuthorizationType`被设置为`AWS_IAM`。如果您现在尝试从外部调用您的 API，您应该会得到一个`403 Forbidden`响应。你的 API 现在是安全的！那么，我们如何向需要调用它的其他内部服务授予权限呢？

现在我们对 API 网关使用 IAM 授权，调用服务需要被授予 IAM 权限来调用它。对于 AWS Lambda 函数，这意味着授予调用目标 API 的权限，并使用相关的 HTTP 动词(get、POST、PUT、PATCH 等)发出请求。)

```
- Effect: Allow
  Action:
    - execute-api:Invoke
    - execute-api:GET
  Resource:
    - arn:aws:execute-api:#{AWS::Region}:#{AWS::AccountId}:*/${self:provider.stage}/*/user/* 
```

Enter fullscreen mode Exit fullscreen mode

这个 IAM 策略片段授予对 Lambda 函数的访问权，以便为路径中带有`/user/`的同一帐户中的任何 API 网关调用 GET 方法。第一个通配符(`*`)代表 API 网关资源 ID。这个是动态生成的，所以我们不想在这里显式的写。第二个通配符代表 HTTP 动词，最后一个通配符代表特定的资源路径。

# 在 HTTP 请求中提供 IAM 凭证

难题的最后一部分是使用正确的凭证进行调用。当我们使用任何 HTTP 请求库(比如 Python 中的[请求](https://2.python-requests.org/en/master/)或者 JavaScript 中的 [axios](https://github.com/axios/axios) )时，我们的 AWS Lambda 函数角色凭证默认不会被传递。为了添加我们的凭证，我们需要[签署 HTTP 请求](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html)。这里需要做一些计算，所以我创建了一个 NPM 模块，它包装了 axios，并使用 Lambda 的角色自动签署请求。该模块名为`aws-signed-axios`，在处[可用。](https://www.npmjs.com/package/aws-signed-axios)

要使用与 API 网关权限相关联的凭证发出 HTTP 请求，只需像这样调用包装器库:

```
const signedAxios = require('aws-signed-axios')
...

async function getUser(userId) {
  ...

  const { data: result } = await signedAxios({
    method: 'GET',
    url: userUrl
  })

  return result
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。总之，我们现在有了一个清晰、可重复的内部无服务器 API 方法，它具有:

1.  一种不用 VPCs 保护 API 网关的方法
2.  我们可以使用 IAM 权限来授予我们希望允许的任何服务
3.  用必要的 IAM 凭证对 HTTP 请求进行签名的简单方法

如果你想探索更多，看看我们的开源无服务器启动项目， *SLIC Starter* :

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [四定理](https://github.com/fourTheorem) / [ slic-starter](https://github.com/fourTheorem/slic-starter)

### 一个完整的无服务器启动项目

<article class="markdown-body entry-content container-lg" itemprop="text">

# SLIC 启动器

[![serverless](img/dbcfae37ba5b6509c63653b97cb3333c.png)](http://www.serverless.com)[![JavaScript Style Guide](img/c079a598eada2d8f8b76fa166c13a74c.png)](https://standardjs.com)[![license](img/097f430ee0bfd5ab892ec0bb360c65ca.png)](https://github.com/fourTheorem/slic-starter./LICENSE)

**跳转到:** [入门](https://github.com/fourTheorem/slic-starter#getting-started) | [快速入门](https://github.com/fourTheorem/slic-starter./QUICK_START.md) | [CI/CD](https://github.com/fourTheorem/slic-starter#cicd) | [架构](https://github.com/fourTheorem/slic-starter#application-architecture) | [投稿](https://github.com/fourTheorem/slic-starter./CONTRIBUTING.md)

* * *

**SLIC Starter** 是针对 AWS 上的生产级**无服务器**应用的完整启动项目。SLIC Starter 使用一种自以为是、务实的方法来构建、开发和部署一个现代化的无服务器应用程序，只有一个简单的总体目标:

> *让您的无服务器应用快速投入生产*

*   【SLIC starter 如何帮助你？
*   [应用架构](https://github.com/fourTheorem/slic-starter#application-architecture)
*   它提供了什么？
    *   [结构](https://github.com/fourTheorem/slic-starter#structure)
    *   [工装选择](https://github.com/fourTheorem/slic-starter#tooling-choice)
    *   [认证](https://github.com/fourTheorem/slic-starter#authentication)
    *   [使用 RESTful API 进行数据访问](https://github.com/fourTheorem/slic-starter#data-access-with-a-restful-api)
    *   [消息传递](https://github.com/fourTheorem/slic-starter#messaging)
    *   [前端](https://github.com/fourTheorem/slic-starter#front-end)
    *   [CI/CD](https://github.com/fourTheorem/slic-starter#cicd)
    *   [测试](https://github.com/fourTheorem/slic-starter#testing)
    *   [监控](https://github.com/fourTheorem/slic-starter#monitoring)
    *   [测井](https://github.com/fourTheorem/slic-starter#logging)
    *   [秘密管理](https://github.com/fourTheorem/slic-starter#secret-management)
    *   [用户账户和授权](https://github.com/fourTheorem/slic-starter#user-accounts-and-authorization)
*   [开始之前！](https://github.com/fourTheorem/slic-starter#before-you-begin)
*   [入门](https://github.com/fourTheorem/slic-starter#getting-started)
*   [第一次成功部署](https://github.com/fourTheorem/slic-starter#getting-to-your-first-successful-deployment)
    *   [为电子邮件设置您的域名](https://github.com/fourTheorem/slic-starter#set-up-your-domain-for-email)
*   [地方发展](https://github.com/fourTheorem/slic-starter#local-development)
*   [前端的后端配置](https://github.com/fourTheorem/slic-starter#backend-configuration-for-front-end)
*   [演示](https://github.com/fourTheorem/slic-starter#demo)
*   [代码风格和语法](https://github.com/fourTheorem/slic-starter#code-style-and-syntax)
*   谁是幕后黑手？
*   [其他资源](https://github.com/fourTheorem/slic-starter#other-resources)
*   [故障排除](https://github.com/fourTheorem/slic-starter#troubleshooting)
*   [投稿](https://github.com/fourTheorem/slic-starter#contributing)
*   [执照](https://github.com/fourTheorem/slic-starter#license)

## SLIC starter 如何帮助你？

1.  无服务器开发涉及到许多决策，这些决策围绕着对大多数人采取哪种方法…

</article>

[View on GitHub](https://github.com/fourTheorem/slic-starter)

* * *

我是四定理的首席技术官，也是人工智能服务的作者。我在推特上的名字是 [@eoins](https://twitter.com/eoins) 。