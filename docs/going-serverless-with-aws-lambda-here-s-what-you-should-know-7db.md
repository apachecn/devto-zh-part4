# 使用 AWS Lambda 实现无服务器？以下是你应该知道的

> 原文：<https://dev.to/ashokisaac/going-serverless-with-aws-lambda-here-s-what-you-should-know-7db>

无服务器现在已经成为主流:这是云计算发展的下一步。
价值主张是您可以:

1)专注于您的应用代码
2)只为实际计算时间和消耗的资源付费。

所有的基础设施都被完全抽象掉了。嗯:)理论上，也许吧...实际上，事情有点不同。

这里是对 AWS Lambda 的一个非常高层次的概述，AWS Lambda 是 Amazon Web Services 的无服务器计算实现。

**运行时上下文:**

*   AWS Lambda 当前的实现在幕后的 Docker 容器中运行您的代码。您的代码可以访问文件系统的 Docker 暂存卷。
*   Lambda 支持运行时的[特定版本。可以构建或填充自定义运行时。](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)

**执行:**

*   Lambda 函数在一段时间后执行时可以有一个“冷启动”时间，因为容器需要启动。可以通过 ping 或健康检查来保持该功能“温暖”。
*   您可以设计自己的函数，以便在直接调用之间重用昂贵的资源(数据库连接等)。
*   Lambdas 具有内置的失败重试功能。重试是基于退出代码或回调错误触发的。
*   Lambdas 可以有一个 DLQ(死信队列)来收集失败的事件，以便进行调查或其他处理。
*   每只羊最多跑 15 分钟，之后就会被杀死。可以在 Lambda 中检查剩余的可用执行时间。
*   分配给 Lambda 的计算和内存量决定了它的运行速度。定价基于 100 毫秒的增量和保留的 CPU/内存(与实际使用的相比)。每个帐户每月前一百万次 Lambda 调用是免费的。
*   AWS Lambda 有并发限制——一次最多执行 1000 个并发 Lambda。如果有必要，可以为关键的 Lambda 函数保留并发性。
*   AWS 步骤函数可用于使用 Lambda 函数构建复杂的工作流。

**触发器**:

*   AWS Lambdas 通常响应于(不断扩展的)[事件源列表](https://docs.aws.amazon.com/lambda/latest/dg/lambda-services.html#intro-core-components-event-sources)而被调用。
*   可以从其他代码、cli 工具或通过 AWS 控制台“手动”调用 Lambdas。
*   调用可以是同步的(请求/响应)，也可以是异步的(触发并忘记)
*   Lambdas 权限是通过 IAM 角色处理的。事件源可能需要获得 IAM 权限才能调用 Lambda 函数。
*   Lambda [与 AWS API 网关服务很好地集成了](https://docs.aws.amazon.com/lambda/latest/dg/with-on-demand-https.html)。外部 API 可以触发 Lambda 函数，而 Lambda 也可以执行认证/授权或充当 API 的代理。
*   Lambdas 可以通过内部应用程序负载平衡器、目标组触发，也可以通过 CloudWatch 计划事件按计划触发。

**VPCs** :

*   Lambdas 既可以在 VPC 之外执行(即只使用其他 AWS 服务，如 S3、DynamoDB)，也可以在 VPC 之内执行(可以访问特定于 VPC 的资源- RDS、EC2 实例等)。
*   当在 VPC 内执行时，每个功能将在 VPC 内提供一个 ENI(弹性网络接口),以便访问资源，比如说在私有子网内。每个子网一个 ENI (& IP 地址)。
*   为了进行 AWS 服务调用，在 VPC 内运行的 AWS Lambdas 将需要互联网接入，即使在私有子网内也是如此，即通过 NAT-ing (NAT 网关或实例)。对于某些服务，您可以通过使用 VPC 端点来解决这个问题。

**其他:**

*   AWS Lambda 通过 KMS(密钥管理服务)为执行过程中的环境变量提供[加密](https://docs.aws.amazon.com/lambda/latest/dg/env_variables.html)。
*   AWS X 射线(跟踪)支持可用于 Lambda 函数。有助于了解哪里引入了延迟。
*   Lambda 日志会自动发送到 CloudWatch 日志流。每次调用都会记录请求 ID、计费时间和使用的内存。
*   有许多工具可以帮助部署到 AWS Lambda。[Serverless.com](http://www.serverless.com)大。AWS 有自己的工具，叫做 [SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) 。

我是无服务器计算的超级粉丝，并且发现 AWS Lambda 是一个非常强大的工具，只要你理解并在它的限制范围内使用它。

有问题吗？评论？顾虑？随时联系我。