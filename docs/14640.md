# 解决 Lambda 冷启动的 4 个步骤

> 原文：<https://dev.to/byrro/4-steps-to-solve-lambda-cold-starts-281l>

如果你喜欢无服务器和 AWS Lambda，你可能也讨厌冷启动。这是一个分步指南，指导您实施一个成功的策略来保持 AWS Lambda 功能预热，减轻冷启动延迟问题。

# 解剖感冒开始了

我们知道我们的 Lambda 函数在微型容器中运行。当一个新的调用到来时，AWS 将启动一个新的容器，将我们的代码和依赖关系加载到内存中，然后为到来的请求提供服务。完成后，该容器将保持活动一段时间(从 5 分钟到几个小时)。如果同时收到一个新的调用，AWS 不需要旋转一个新的容器。

上面故事中的第一次祈祷是“冷启动”。第二个处于“温暖状态”。冷启动的问题是它增加了延迟和不一致性，这对用户体验是有害的。

# X-Lambda 力量前来救援

[![Ice cubes on fire](img/72e02167b6eff56296c8d46173e31c58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4hUDC2SD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hoatgeadvkwmvfulemc6.jpg)

我们最近启动了一个名为 X-Lambda 的开源项目。简而言之，它是这样做的:

1.  你的兰姆达斯调用历史的轨迹；
2.  使用统计数据来预测您的函数的短期容器需求；
3.  在预定的基础上同时调用 Lambdas，以保持正确数量的容器被加热；

下面是在您的 AWS 帐户中实施它的一步一步的指南。在我们开始之前，继续从我们的[公共回购](https://github.com/dashbird/xlambda/)下载或克隆项目。

> 请注意，该项目仍处于 alpha 阶段，因此我们不建议将它用于关键任务的生产应用程序。

## 1。它的功能是保暖

[![Fire](img/66587cb88843c64c4212681e33651f27.png "Fire")](https://res.cloudinary.com/practicaldev/image/fetch/s--HW15a0sW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/dashbird/xlambda/raw/mastimg/fire.png)

在`xlambda-config.yml`文件中，在`lambda_functions`下，列出你要保温的功能。

X-Lambda 依赖于来自 CloudWatch API 端点 [GetMetricsData](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_GetMetricData.html) 的 *ConcurrentRequests* 度量。这个度量只适用于具有[保留并发设置](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html)的函数，所以您在这里列出的函数需要有这个设置。

还要记住，为了让您的函数保持温暖，X-Lambda 需要不时地调用它们(默认情况下每 15 分钟一次)，这自然要花钱。我们建议把注意力集中在你真正需要保持温暖的羊肉上，否则就是浪费资源。

## 2。自定义缩放选项

在同一个`xlambda-config.yml`文件中，自定义缩放选项。

在`scaling`下，你会发现三个参数。它们将全面应用于您的所有职能:

*   `min_containers`:每轮保温的最小容器数；
*   `max_containers`:同上，但为最大阈值；
*   `max_concurrency`:限制 X-Lambda 将发送给你的 Lambdas 的并发请求的数量，允许你控制消耗你的并发配额的多少；

在函数列表中，您还可以为每个函数设置这些参数，在每个函数的基础上定制 X-Lambda 行为。

## 3。对我们的函数代码做了微小的调整，以处理升温请求

X-Lambda 会定期调用你的函数来保持它们的热度。它将发送这个 JSON 负载:

```
{  "xlambda":  {  "action":  "warm_up",  "settings":  {  "startup_time":  1234  #  Milliseconds  }  }  } 
```

我们建议在 Lambda 处理程序中尽早设置条件检查，以便在 X-Lambda 调用代码时跳过代码的执行。

您还需要确保您的函数在终止执行之前休眠了在`startup_time`参数中设置的时间(值以毫秒为单位)。这对于确保加热后的容器在加热过程中不会被重复使用非常重要。

## 4。确保一切按预期运行

AWS Lambda 本身就是一头猛兽。它巨大的可伸缩性和特性(例如，超时和内存限制会带来麻烦)加上任何应用程序都可能出错的一大堆问题，使得很难监控和维护任何无服务器应用程序的健康。

为此，首先[在 Dashbird 上创建一个免费账户](https://dashbird.io/register/)(不需要信用卡)。然后，[将 Dashbird](https://dashbird.io/docs/get-started/quick-start/) 与你的 AWS 账户整合。

并且 *voilá* ，Dashbird 会自动开始监控你的 AWS Lambda 功能。我强烈建议[设置警报渠道](https://dashbird.io/docs/user-guide/integrations/)(电子邮件和/或空闲渠道)警报，这样当事情变糟时[就能得到通知](https://dashbird.io/docs/user-guide/alerting/)。

# 包装完毕

X-Lambda 还处于早期阶段，有很大的改进空间。我们欢迎建议和贡献。欢迎您在[项目问题页面](https://github.com/dashbird/xlambda/issues)与我们互动。如果你想关注项目进展，[订阅我们的时事通讯](https://dashbird.io/#mc-embedded-subscribe-form)(页面底部)。