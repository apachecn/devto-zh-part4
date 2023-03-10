# 介绍记录自定义指标的更好方法

> 原文：<https://dev.to/theburningmonk/introducing-a-better-way-to-record-custom-metrics-3331>

许多客户问我"*如何从 Lambda 记录定制指标？*”。

一般来说，您可以:

*   同步发布定制指标**——例如，在调用结束时发送它们。**
***   通过首先将自定义指标**写入 stdout，然后从 CloudWatch 日志中提取出来，异步发布自定义指标**。**

 **### 同步发送自定义指标的问题

同步方法增加了调用的延迟。当我们的用户体验到这些额外的毫秒数时，这可能会特别成问题。例如，如果用户正在等待 API 响应。

[![](img/5fa938a4875f6c576579cddef18090c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MMdO6Xqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a26d65e330.png)

单独来看，延迟可能可以忽略不计。CloudWatch 指标通常在几十毫秒内做出响应。这对大多数人来说是可以接受的。但是当函数通过 API Gateway 相互调用时，它们可以快速复合。

[![](img/66b8cf7f297d5d8201f77af5e7475574.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wW8SsKed--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a26ed2d82d.png)

此外，服务在它们的集成点附近是最脆弱的——即当它们对其他服务进行网络调用时。将指标发布到 CloudWatch 引入了另一个需要加强的集成点。

如果 CloudWatch 出现宕机，您肯定还是希望系统保持运行，对吗？同样，如果 CloudWatch 经历了响应时间延长，那么您不会希望您的函数因此超时！

因此，尽管这种方法也有缺点，但我通常更喜欢异步记录定制指标:

*   查看最新的度量数据会有额外的延迟。
*   它向 CloudWatch 日志发送了更多数据，这意味着成本增加

[![](img/d22d776b810d7cfd51c3e8393e32ea55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dg2uRUhu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a2701910ac.png)

*   它引入了复杂性，因为您需要一些东西来处理日志并将它们转换成指标。

### 异步发送自定义指标

在简单的情况下，如果您只有很少的自定义指标， [CloudWatch 指标过滤器](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringPolicyExamples.html)是一个不错的选择。然而，这种方法不能随着复杂性而扩展——当您有许多功能和自定义指标时。

相反，您可以使用 Lambda 函数。

为了让您更容易地异步记录定制指标，我向[无服务器应用程序库](https://serverlessrepo.aws.amazon.com/applications)发布了一个新的应用程序。你也可以在 GitHub [上查看源代码。](https://github.com/theburningmonk/SAR-async-lambda-metrics)

#### 入门

你可以通过 AWS 控制台[这里是](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:374852340823:applications~async-custom-metrics)，点击`Deploy`按钮并按照说明进行部署。

[![](img/14685cbd66307b1093668d304ebc40e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MYfTidr2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a272c093e6.png)

或者您可以使用 AWS SAM 将其作为 CloudFormation 堆栈的一部分进行部署:

[![](img/f55088815ecf204e7f3c89a8ed7b473d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mCqij3rD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a274465d8d.png)

您可以通过 CloudFormation 或无服务器框架完成同样的工作。不过，您需要首先添加下面的`Transform`。关于如何用无服务器框架做到这一点的更多细节，请阅读这篇[文章](https://dev.to/theburningmonk/how-to-include-serverless-repository-apps-in-serverless-yml-52md)。

```
Transform: AWS::Serverless-2016-10-31 
```

我们昨天在 Twitch 上发布了这款新应用[。您可以前往 21:00 mark，看看如何通过 CloudFormation 配置一切，包括首先将所有 CloudWatch 日志组订阅到 Kinesis 流，然后再将该应用订阅到该流。](https://www.twitch.tv/videos/457316850)

#### 记录自定义指标

部署完成后，您将能够通过写入以下格式的`stdout`来记录自定义指标:

`MONITORING|<value>|<unit>|<metric_name>|<namespace>|<dimensions>`

其中:

*   `value`是一个`float`
*   `unit`可以是任何一个[允许的 CloudWatch 公制单位](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_MetricDatum.html)
*   `namespace`:自定义指标将出现在此名称空间下，例如

[![](img/a09265a517b9c3f82ff11e27c3d270ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MsJRjGXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a2762c805a.png)

*   `dimensions`:逗号分隔的键值对，如`service=content-item,region=eu-west-1`

这些消息将在 CloudWatch metrics 中作为定制指标进行处理和发布。所有这些都不会增加调用的延迟！

### 未来作品

我希望你喜欢这个新应用，并请随时通过 GitHub [问题](https://github.com/theburningmonk/SAR-async-lambda-metrics/issues)提出改进建议。我有一些让它更有用的想法:

*   在调用结束时解析`REPORT`消息，并将`Billed Duration`、`Memory Size`和`Memory Used`转换成度量:

[![](img/821479592a826f727017ca1d62c8b208.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LMj0MHMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/07/img_5d3a278aeab9a.png)

*   由于 AppSync 不向 CloudWatch 报告解析器指标，因此我们可以解析[解析器日志](https://docs.aws.amazon.com/appsync/latest/devguide/monitoring.html)并将解析器持续时间报告为指标。

[![](img/9146b78a9f58c8748bf4f1edb61ff083.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bk6FK0kn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://theburningmonk.com/wp-content/uploads/2019/03/img_5c8ca195210d5.png)

嗨，我的名字是**崔琰**。我是一个 **[AWS 无服务器英雄](https://aws.amazon.com/developer/community/heroes/yan-cui/)** 和 [**量产无服务器**](https://bit.ly/production-ready-serverless) 的作者。我已经在 AWS 中运行了近 10 年的大规模生产工作负载，我是一名架构师或首席工程师，涉足从银行、电子商务、体育流媒体到移动游戏等多个行业。我目前是一名专注于 AWS 和无服务器的独立顾问。

你可以通过[邮箱](//mailto:theburningmonk.com)、[推特](https://twitter.com/theburningmonk)和 [LinkedIn](https://www.linkedin.com/in/theburningmonk/) 联系我。

雇用我。

介绍记录自定义指标的更好方法的帖子[首先出现在 theburningmonk.com](https://theburningmonk.com/2019/07/introducing-a-better-way-to-record-custom-metrics/)[的](https://theburningmonk.com)上。

[![](img/5d0d662f80db3615564691b2e2917b97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ByJ_3-TN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/theburningmonk/%257E4/rvnQRIDr9BQ)**