# 在 AWS 免费层混日子

> 原文：<https://dev.to/cloudcostsio/getting-by-on-the-aws-free-tier-4gjg>

所以你想尝试一个新的 AWS 账户。你听说过免费层，并想尝试 AWS 上的一些功能。这是给你的指南！

AWS 提供了一个免费层，允许在长达一年的时间内完全免费使用某些服务。如果您正确地组合这些服务，您甚至可以在这一层上运营您的业务。你可以在 AWS 上完全免费运行一个 web 应用程序长达一年。根据你想学什么或你的技能组合，有几种方法可以做到这一点。

### 建筑

您可以免费运行 t2.micro 实例长达一年。如果你喜欢以更手动的方式配置你的应用程序，那么只需设置一些 SSH 密钥，你就可以将你的应用程序部署到你想部署的服务器上。但是，如果您想使用 docker 和一些较新的 devops 工具，请查看下一部分。

AWS 有一个叫 ECS(弹性容器服务)的服务，可以让你在 AWS 上运行 docker 容器。最初使用这项服务可能会有点令人生畏，但如果您预算有限，它的一大好处是管理平台是免费的。您只需为您使用的底层 EC2 实例付费。

要在 ECS 上免费运行，您只需在自动扩展组中配置一个 t2.micro 实例，并在空闲层中放置一个小磁盘。如果您浏览 AWS 的第一个运行示例，只需将自动缩放组更改为带有 t2.micro 的单个实例。

ECS 使用其他资源，如 Cloudwatch 日志或潜在的负载平衡器。所有这些都有一个自由层选项。ECS 的[教程](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_GetStarted.html)可以带你了解细节。

你可以在你的 web 应用前面的 AWS 上免费运行一个负载均衡器。其中最棒的一点是，你还可以获得 SSL 证书，这样你就不必为你的应用程序购买证书或其他任何东西。

RDS 还为最小的数据库实例大小提供了一个免费层。这对于开发 Rails 或 Django 应用程序来说是完美的，在您成长之前，您不会注意到任何性能问题。

免费层甚至有更多的好东西。Codebuild 有 100 分钟的构建时间，这取决于你一个月构建了多少。SES 还允许你每月免费发送大量电子邮件(62，000)。

## 概述

最终的架构如下所示:

*   ALB 处理流量
*   证书的 ACM
*   用于管理部署的 ECS
*   EC2 托管 ECS 集群
*   用于查看日志的 Cloudwatch 日志
*   发送电子邮件的 SES
*   数据库的 RDS
*   CI 的代码构建

不要脸的 plug:如果你对在免费层上运行东西感兴趣，并且不想担心设置计费报告，请通过 [https://cloudcosts.io](https://cloudcosts.io) 来访问我们。谢谢！