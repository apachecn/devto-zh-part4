# 无服务器实时通信(WebSockets)

> 原文：<https://dev.to/oskarkaminski/serverless-with-real-time-communication-websockets-2c6j>

## 免责声明

> 本文是在无服务器架构上构建 WebSockets 的深度指南。

如果你不熟悉 WebSockets，请先[什么是 WebSockets？](https://pusher.com/websockets)
如果你从未用过 Serverless，请先阅读[什么是 Serverless？](https://serverless-stack.com/chapters/what-is-serverless.html)

## 简介

去年，我的团队构建了 100%无服务器的移动应用程序，现在帮助数百名开发人员维持快乐健康的团队。

* * *

检查团队健康检查如何帮助开发人员

[![oskarkaminski image](img/5d161dc9f86269194b5de51f9153ecf9.png)](/oskarkaminski) [## 工具，帮助我建立健康💕和表演团队👩🏾‍💻👨🏼‍💻

### 奥斯卡 6 月 1 日 194 分钟阅读

#team #career #performance #webdev](/oskarkaminski/tools-that-help-me-build-healthy-and-performing-teams-237e)

* * *

在我们开发了这个应用的第一个版本后，感觉有些不对劲。想象一下这个应用程序，其中每个屏幕每次都必须调用 Lambda 函数来获取数据，如果有人加入你的团队，你必须导航出去并再次进入才能看到变化。

我们意识到，是时候建立实时联系了。

我们已经尝试了更简单的方法(AppSync)，并且...更灵活的方式:)(API Gateway v.2)。本指南介绍了更复杂的架构——使用 API 网关。

## 无服务器 REST API 和无服务器 WebSockets 的区别。

让我们首先回顾一下典型的无服务器架构是什么样子，然后看看当我们添加套接字层时它是如何发展的。

### 使用 REST API 的无服务器

在无服务器上处理 REST API 请求是一个简单的过程，在最常见的情况下，利用 4 层——每层负责自己的功能(如下图所示)。

1.  用户向 API 网关发送带有`Authorization`报头的请求。
2.  API 网关使用现成的`Cognito User Pool authorizer`对用户进行身份验证。Amazon Cognito 授权(或拒绝)访问，并将用户属性作为`uuid`或`email`传递给请求上下文。
3.  API 网关执行相关的 Lambda 函数和用户属性。
4.  Lambda 函数向数据库发出所有必要的请求，进行数据转换，并向 API 网关返回响应
5.  API 网关将响应返回给用户。

对于每个请求，每次都会发生相同的过程，包括身份验证。

[![](img/67f86bb313c88474a95a0e9bfa06c793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--daPOo2is--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8p8we1r1qaoldqnykyok.png)

很简单，对吧？

### WebSockets on Serverless

不过 WebSocket 连接更加复杂:)

这里的用户只被认证一次——在他们最初的`UPGRADE`请求期间。如果认证过程成功，那么只要任何一方不关闭套接字连接，套接字连接就建立。

向 WebSocket 协议发出后续请求的用户不提供他的声明，因为连接是有状态的，并且服务器(这里是 API 网关)保存着关于发送者的信息。

下图显示了当设备连接到 API Gateway WebSocket API 时会发生什么，以及稍后当一个用户调用 API 时会发生什么(在这种情况下，用户正在投票，他的队友会得到通知)。

[![](img/14e4b62c3531318fac2dd05f7c3b14a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u20GjTiq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7bhbngf12tk5dyupmwob.png)

关于这个架构有一些有趣的事实。

#### 我们正在实现自定义授权器。

`API Gateway WebSocket API`不提供与`Cognito User Pool`的现成集成。因此一个`Custom Authorizer`必须由我们自己来实现。

自定义授权器是一个 Lambda 函数，它使用从用户所在的授权服务器获取的公共 rsa 密钥来验证 JSON 令牌，然后通过在响应中设置适当的`IAM policy`来授予访问权限。

#### 我们正在存储 socket id 以备后用。

当访问被授予时，我们的责任是将`socket id`保存到数据库，如`DynamoDB`。

在上述架构中，套接字 id 保存在`ConnectionsTable`和`ProfilesTable`中。

#### 授权人只执行一次。

`Authorizer`和`onConnect`都是 lambda 函数，当用户连接到 socket API 时只执行一次。这使得对套接字的后续请求更快。

#### 我们可以从任何地方调用 socket，甚至是 AWS 之外。

我们可以从另一个服务如`Lambda Function`、`EC2`甚至 AWS 外部通知套接字。

我们通过向 API 网关的`@connections` route 发送一个`POST`请求来实现这个目的，这个请求带有我们想要在路径中通知的套接字的 id。
如果我们想要通知 100 个套接字，我们必须发送 100 个 POST 请求，这可能有点令人担忧，但迄今为止，它在我们的生产应用程序中运行良好。

#### 我们正在签署每一个请求。

对`API Gateway WebSocket API`的每个请求都必须签名。这是为了确保 API 网关，我们有一个通过套接字向用户推送数据的权限。

不久前它还需要很多步骤，但是多亏了我们最近发布的 npm 包 [aws-request-signer](https://www.npmjs.com/package/aws-request-signer) 你几乎可以不费吹灰之力就做到。

## 给包起来

我们已经讲述了典型的无服务器架构，然后我们展示了当我们添加实时通信时它是如何发展的，最后我们讲述了一些最有趣的亮点。

如果你想去无服务器，或需要添加实时通信，随时给我发短信，或雇用我的服务，这样你就可以享受全自动无服务器设置与最佳做法在一周内。

如果您不同意任何部分，请评论您的反馈。如果您不想错过更多来自生产应用的深度内容，请关注。

感谢阅读！