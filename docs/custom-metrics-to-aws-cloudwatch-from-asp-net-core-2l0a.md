# 来自 ASP.NET 核心网的 AWS CloudWatch 自定义指标

> 原文：<https://dev.to/codeopinion/custom-metrics-to-aws-cloudwatch-from-asp-net-core-2l0a>

[![CloudWatch Custom Metrics](img/b015a6376944d9fde76e39c92b9563df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q-qBR78W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeopinion.com/wp-content/uploads/2019/08/aws-cloudwatch-logo-png-transparent-265x300.png)

我当时在玩 AWS CloudWatch，很好奇会从 ASP.NET 核心发送自定义指标。特别是 HTTP 请求的执行时间。

## AWS SDK

我创建了一个简单的中间件，它在调用管道中的下一个中间件之前启动秒表。当它返回时，停止秒表并将数据发送到 CloudWatch。

首先是添加相关的 NuGet 包。