# 如何通过 API 网关将 SNS 连接到 Kinesis 进行跨帐户交付

> 原文：<https://dev.to/theburningmonk/how-to-connect-sns-to-kinesis-for-cross-account-delivery-via-api-gateway-2nnc>

在 DAZN(我不再工作的地方)，团队与许多第三方提供商合作。他们经常需要在不同的 AWS 账户之间同步数据。SQS 产品升级和技术支持服务是这些跨帐户交付的主要机制，因为:

*   这是本组织内的既定模式
*   DAZN 工程师和第三方工程师都熟悉 SNS 和 SQS，以及使用 Lambda 处理 SQS 事件
*   Lambda 根据负载自动调整并发执行

当然，正如我[最近写的](https://lumigo.io/blog/amazon-eventbridge-a-new-era-of-saas-integration/) , [EventBridge](https://aws.amazon.com/eventbridge/) (以前的 CloudWatch Events)也是跨账户交付的一个很好的选择。稍后将详细介绍。

### 问题

DAZN 在全球拥有数百万订户，在直播活动期间为超过 100 万的并发观众提供服务。它的交通模式是非常尖锐的，并围绕这些现场体育赛事。

[![](img/e10b98f887c1285186bb896a6003bba5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--If9XvC-9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e35470372.png)

DAZN 的很多微服务都生活在同一个 AWS 账号里(他们还在向每个环境每个团队一个账号迁移的过程中)。所以这些微服务争夺相同的区域限制，比如并发 Lambda 执行的数量。

其中一个微服务从第三方 AWS 帐户获取事件，并立即将它们推送到 Kinesis 流。

[![](img/22a7dc693e222fea5771e583f11c5133.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yXgPg0aP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e38c7ab32.png)

这种微服务在体育赛事开始前会经历大流量爆发。不幸的是，许多其他微服务也同时经历这些峰值！

因为 SQS 会自动调整并发执行的数量，Lambda 函数(您在上面看到的)使用了太多的可用并发。它会在该区域中对自身以及同一区域中的其他函数引发 Lambda 节流事件。

找到合适的解决方案有时间压力，所以我在 DAZN 的哥们伸出了手，我们集思广益了一些解决方案。

### 方案

#### 解决方案 1–λ并发限制

为 SQS 函数设置保留并发将是最简单的解决方案。然而，你必须处理这个决定的后果。Lambda 并发限制和 SQS 存在问题。我建议你阅读[这篇](https://medium.com/@zaccharles/lambda-concurrency-limits-and-sqs-triggers-dont-mix-well-sometimes-eb23d90122e0)和[这篇](https://medium.com/@zaccharles/reproducing-the-sqs-trigger-and-lambda-concurrency-limit-issue-f4c09d384a18)的帖子，了解更多相关信息。根本问题是 SQS 轮询器(由 Lambda 管理)的数量与函数的并发限制之间存在脱节。

如果 SQS 轮询器在尝试向您的函数转发消息时被反复抑制。然后，在您的函数有机会处理它之前，消息可以被重定向到 DLQ。这将是最坏的情况。

即使消息仅被抑制一次并在可见性超时后被处理，它仍然会造成巨大的破坏。这种延迟(可见性超时)允许后续事件在 Kinesis 流中先于该原始消息。这个排序问题现在已经存在，因为正常的 SQS 队列不保留事件的顺序。然而，它只影响了不到 1%的客户，团队认为这不是一个大问题。随着节流和重试，它成为下游功能的一个更紧迫的问题。

#### 解决方案 2–使用单独的 AWS 账户

将这个微服务转移到它自己的帐户中可以缓解争用问题(对于并发执行)。这样做还有其他的好处，这是一个已经在进行中的任务。然而，第三方供应商目前不允许从另一个 DAZN 拥有的 AWS 帐户订阅 SQS。

#### 解决方案 3–切换到 EventBridge

切换到 EventBridge 将是另一种选择。SNS 支持的跨账户交付目标很少——HTTP、SQS 或 Lambda。EventBridge 可以向[交付更多的目标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)，包括 Kinesis 流、ECS 任务、Step 函数等等。然而，这需要第三方做出重大改变。或者在主帐户中创建一个 DAZN 端的 sink，然后使用 EventBridge 将它分散到其他帐户(见下文)。

[![](img/7551f9fa43a475c9acd3ad19db7b99b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yy_lOfS0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e3a76365a.png)

这可能是一个可行的解决方案，并提供了很大的灵活性。但它也面临一些挑战，例如:

*   如果第三方没有更改为 EventBridge，那么 SQS 函数仍然存在相同的并发问题。
*   将多个团队转移到一个不熟悉的服务需要协调。

最重要的是，这不是一个简单的改变，而且还有时间压力。

#### 解决方案 4–从 SNS 直接转到 Kinesis(通过 API 网关)

不用经过 SQS 和 Lambda，你可以通过 API 网关服务代理直接进入 Kinesis。这意味着我们将为第三方 SNS 主题订阅 HTTPs 端点，而不是 SQS。

[![](img/ba70be98d92f23c090e52ecc4ac05cff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EITLB8d0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e3b8838d6.png)

这将λ从等式中完全移除。然而，API 网关有它自己节流和争用问题。默认情况下，API 网关的区域限制为 10，000 个请求/秒(适用于所有 API)。幸运的是，这是一个软限制，可以通过支持票提高。

这是一个有趣的想法，所以我构建了一个简单的概念验证来看看它是如何工作的。你可以在 GitHub [这里](https://github.com/theburningmonk/sns-to-api-gateway-demo)找到演示项目的源代码。

### 通过 API 网关将 SNS 连接到 Kinesis

有几件事需要注意:

*   当您为一个端点订阅 SNS 主题时，SNS 会首先向端点发送一个`POST`请求来确认订阅。这个[页面](https://docs.aws.amazon.com/sns/latest/dg/sns-http-https-endpoint-as-subscriber.html)解释了确认流程。
*   `POST`请求包含一个 JSON 负载，如下所示。您需要向`SubscribeURL`发送一个`GET`请求来确认订阅。

    .gist table { margin-bottom: 0; }
*   您需要为 Kinesis 流订阅一个 Lambda 函数来执行请求确认。

*   奇怪的是，`POST`请求使用了内容类型`text/plain`。因此，您需要在 API 网关中为`text/plain`定制一个请求模板映射。

[![](img/be33dccab84e0e54025f5c11a4bbf817.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cLAx3b1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e3ff3cc20.png)

*   您还需要编写一些定制的 VTL 代码来将请求映射到 Kinesis `PutRecord`动作。

[![](img/5277f23d0c4a4260a36b6765f4f3b50e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7OKkI950--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4128e861.png)

[![](img/fbeee37b91e125f2e327d7146cf93abc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dri9zKrC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e41e65ce3.png)

如果你使用无服务器框架，那么你可以使用 [Horike](https://twitter.com/horike37) -san 的[无服务器-apigateway-service-proxy](https://github.com/horike37/serverless-apigateway-service-proxy) 插件来帮助你设置服务代理。

该插件使得为 API 网关设置服务代理变得容易。我所需要的只是添加一些如下所示的配置。注意，我使用了`Fn::Sub`将流名编入 VTL 代码中，以避免硬编码。

[![](img/2ff22dacffe346257670f0032e8b1b9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FemLQA5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e43141a69.png)

这个配置向 API 添加了一个`/kinesis`端点，它将来自 SNS 的请求转发给我们的 Kinesis 流。

在演示项目中，还有订阅 Kinesis 流的 Lambda 函数。该功能负责确认订阅请求。

[![](img/05f643b6475c45f8602b4d8b3fd85e42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wCxF74MH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4439d938.png)

然而，这个函数只需要运行一次——当 SNS 主题发送它的确认请求时。它将继续接收所有后续事件，并忽略它们。那看起来太浪费了！

确认订阅后功能可以自行禁用怎么办？

[![](img/08156ee9ab2b8bd7fa11f48c88fd17ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tLa-NdMm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e456e9608.png)

您可以通过禁用该函数的事件源映射来做到这一点。

[![](img/36a732cdadeabab883def95324c39b00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YtB3nqmk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e46633b3d.png)

当然，为此您需要相关的 IAM 权限。

[![](img/53e28be6d7edcf2e18835d246fc4cd53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g7U7WPwu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4797bbbc.png)

#### 试用中！

部署项目后，转到 SNS 主题。针对`/kinesis`端点创建新的订阅。

[![](img/a4342bddb7c40f3a374aec333d6f51db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BgmScZpe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e48bc0b36.png)

在 Kinesis 函数的日志中，您应该会看到来自 SNS 的一个`SubscriptionConfirmation`事件。

[![](img/d6b198f65a0a41c243b16d27eb91aa5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AhV1k4jx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e49b8228c.png)

之后，您应该会看到日志，指出该函数正试图禁用其 Kinesis 事件源映射。

[![](img/7ec7cc7def33b98aeeb475708b209d78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8aE6UaCA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4abc8140.png)

[![](img/64199337588ab06881676b268cd9ee05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IAZMNR3---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4b7ddc84.png)

现在去 Lambda 控制台，找到 Kinesis 功能。单击 Kinesis 事件源，您应该会看到它的状态更改为`Disabled`。

[![](img/450159bc0489d7d7fd10a974ca11df67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NRD0D_mG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4c931403.png)

同时，如果您返回到 SNS 主题，您应该会看到订阅已被确认。如果你发布一个消息到这个主题，那么这个消息会被记录在流中，但是不会调用 Kinesis 函数。

[![](img/9c796b04ac9d64da7102f06a88b4d648.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KqpUl1FE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d34e4d9b4e4e.png)

就这样，我希望你喜欢！这是一个愉快的周末的有趣的小思想实验和演示项目。

[![](img/9146b78a9f58c8748bf4f1edb61ff083.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bk6FK0kn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/03/img_5c8ca195210d5.png)

嗨，我的名字是**崔琰**。我是一个 **[AWS 无服务器英雄](https://aws.amazon.com/developer/community/heroes/yan-cui/)** 和 [**量产无服务器**](https://bit.ly/production-ready-serverless) 的作者。我已经在 AWS 中运行了近 10 年的大规模生产工作负载，我是一名架构师或首席工程师，涉足从银行、电子商务、体育流媒体到移动游戏等多个行业。我目前是一名专注于 AWS 和无服务器的独立顾问。

你可以通过[邮箱](//mailto:theburningmonk.com)、[推特](https://twitter.com/theburningmonk)和 [LinkedIn](https://www.linkedin.com/in/theburningmonk/) 联系我。

雇用我。

关于如何通过 API 网关将 SNS 连接到 Kinesis 进行跨账户交付的帖子[最早出现在 theburningmonk.com](https://theburningmonk.com/2019/07/how-to-connect-sns-to-kinesis-for-cross-account-delivery-via-api-gateway/)[的](https://theburningmonk.com)上。

[![](img/9d7b1d08c14069e910cddfc0aaff9b3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HF8ho8oz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/theburningmonk/%257E4/hJAbM8_JNr8)