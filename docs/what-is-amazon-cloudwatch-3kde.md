# 亚马逊 CloudWatch 是什么？

> 原文：<https://dev.to/sensedeep/what-is-amazon-cloudwatch-3kde>

亚马逊 CloudWatch 已经存在了一段时间，但经常被误解。这篇文章有助于更好地理解这项至关重要的服务。

Amazon CloudWatch 是 AWS 服务和您的云应用程序的统一监控服务。AWS CloudWatch 从 EC2 实例、RDS 数据库、VPC、Lambda 函数和许多其他服务等资源中收集和存储操作指标和日志文件。使用 CloudWatch，您可以监控您的 AWS 帐户和资源，并生成一系列事件或针对特定条件触发警报和操作。

Amazon CloudWatch 提供了对 AWS 资源的可见性，以监控资源利用率、应用程序性能和运行状况。您可以使用这些见解来管理您的应用程序并保持其平稳运行。

## 引擎盖下

CloudWatch 由两种截然不同的服务组成，以通用名称“CloudWatch”进行推广。

*   一种度量服务，用于捕获和管理资源性能和运营度量。

*   一种日志服务，用于捕获、存储和管理服务和应用程序日志。

度量服务提供资源度量数据捕获、存储、仪表板、事件过滤和警报。事件服务被标记为 CloudWatch Events，警报服务被标记为 CloudWatch Alarms。

日志服务名为 CloudWatch Logs，提供日志数据捕获、存储、归档和基本的日志查看和查询功能，称为 CloudWatch Logs Insights。

[![](img/bc4d796e48f3943075ac087b188b3d6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kW80Kuqe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/blog/cloudwatch-components.png)

### 命名混乱

CloudWatch 令人困惑，因为指标和日志作为单一服务呈现，而实际上它们是两个不同的服务。将日志记录服务命名为“CloudWatch Logs”而不命名度量服务，这种混乱会加剧。然而，更多的困惑来自于这样一个事实，即这些服务中的每个功能都有自己的品牌名称:(CloudWatch 事件、CloudWatch 警报和 CloudWatch 日志洞察)。除此之外，还有大量以“云”开头的 AWS 名称，如 CloudFront、CloudSearch、CloudHSM、CloudFormation、CloudTrail，还有 Cloud9，这就有点像狗的早餐了。命名肯定不是亚马逊的强项！

无论如何，有宝石在下面，CloudWatch 是几乎所有基于 AWS 的解决方案的关键组件。

## CloudWatch 日志服务

Cloudwatch Logs 是亚马逊为其服务和您的应用程序提供的基础统一日志解决方案。它提供具有基本管理功能的日志数据捕获、存储和保留策略。

CloudWatch 日志的主要价值是统一的日志捕获和存储库。当 AWS 服务发出日志数据时，它们利用 CloudWatch 日志作为它们的日志服务。为日志数据提供单一、一致的捕获和访问点是非常宝贵的。许多 AWS 服务会创建日志数据，并将其导出到 CloudWatch 日志中进行存储，包括:Lambda、VPC 流日志和 RDS。

应用程序可以通过 EC2 CloudWatch 代理或直接通过 AWS API 或 CLI 将其日志发送到 CloudWatch 日志。许多日志框架都有插件，使得这成为一个不需要编码的命题。

CloudWatch 日志可以将日志流式传输到其他目标进行处理，包括 Lambda 函数或 AWS ElasticSearch。

虽然 CloudWatch Logs 确实具有简单的查看器和查询功能，但这两者都是基本产品，大多数用户使用第三方日志记录解决方案来补充它们，这些解决方案接收 CloudWatch Logs 数据，然后提供增强的可视化和分析工具。比如: [SenseDeep](https://www.sensedeep.com/) 。

[![](img/756cf36ccaacc5b1579c0a5cd0fbf24c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bYco2CS4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/blog/cloudwatch-logs.png)

### 记录数据

CloudWatch 日志将日志数据作为带有时间戳的消息接收。该消息可以被格式化为纯文本、JSON 或任何其他期望的格式。CloudWatch Logs 对日志消息的格式理解有限，通常将消息视为纯文本。

通过命名日志组和流的两级层次结构来存储和访问日志。这些可以被认为是文件系统文件夹和文件。日志在范围上是区域性的，即它们是通过捕获它们的 AWS 区域存储和访问的。没有跨所有区域的日志全局视图。

日志(组)可以指定一个保留时间，在此时间之后，事件将从日志组中删除。默认情况下，事件永远不会过期，这可能代价很高，尤其是当日志分散在全球不同的 AWS 区域时，日志会丢失。对于 CloudFront 日志来说，这可能是一个问题，因为日志增长很快，并且存储在离服务内容的 AWS 存在点最近的区域。

### CloudWatch 日志浏览器

CloudWatch Logs 提供了一个基本的日志查看器。对于单个日志流，您可以一次查看一页日志事件。
您必须手动选择要显示的日志流，它是日志事件数据的一部分。AWS 将为许多服务定期创建新的流。例如:每次 Lambda 函数执行冷启动时，都会创建一个新的日志流。

[![](img/424ff0e8880238d3aa03fcc9b3c1a963.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rDF3ge8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/blog/cloudwatch-logs-viewer.png)

日志数据以时间戳和消息的形式显示，内嵌 JSON 字符串的格式也很好。日志数据一次呈现一页。您可以滚动查看更多数据，但下一页需要等待 3-5 秒。查看器有一个文本过滤器，因此您可以通过简单的文本模式来过滤日志数据，还有一个日期选择器来指定事件的日期范围。但是，查看器缺乏自动显示最新日志事件的“实时跟踪”能力。

查看器不能自动将来自多个流的事件合并到一个组中。您必须依次检查各个流，以显示所需的日志事件。

当通过日志数据调试应用程序时，这些 CloudWatch 日志限制可能会使查看器令人沮丧且使用缓慢。您可能希望考虑一个更快的增强型 CloudWatch 日志查看器，如[https://www.sensedeep.com](https://dev.toSenseDeep)来加速您的日志浏览和分析。

### CloudWatch 日志洞察

Insights 是一个交互式日志查询工具，因此您可以可视化和分析日志数据。查询可以过滤和聚合日志数据，以创建可视化日志数据的时间序列图，或者发布到 CloudWatch 仪表板。Insights 是 CloudWatch 日志服务(2018 年末交付)的后续补充。

[![](img/b974a4367fbe01c2e6082ff5ddf26125.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pK0buFWj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/blog/cloudwatch-insights.png)

## 云观察指标服务

CloudWatch 指标服务包括:

*   从 AWS 服务、CloudWatch 日志洞察和用户应用程序中获取的指标
*   显示时间序列度量数据的仪表板
*   度量值改变时触发警报
*   度量值变化时生成的事件流

### 度量标准

CloudWatch 指标是由 AWS 服务、CloudWatch Logs Insights 或用户应用程序发布到 CloudWatch 的时序数据点。这些指标有一个名称、时间戳、名称空间和零个或多个数据的键/值对。

AWS 为其许多服务生成指标，包括:EC2、EBS、RDS、SQS、SNS。这些指标传达了错误情况和性能比率。EC2 等一些服务免费提供基本指标，并提供详细的监控指标作为选项。

## 仪表盘和图表

Amazon CloudWatch 仪表盘是可定制的页面，您可以对其进行配置，以便从一个位置监控您的资源。仪表板可以在一个页面上包含多个图表和警报，并且可以聚合来自多个 AWS 区域的指标。

您可以为 AWS 帐户的不同视图创建多个仪表板。

### 云手表报警

CloudWatch 警报持续监控 CloudWatch 指标，并在一个或多个指标超过指定阈值时发出警报。
警报可以向 AWS 简单通知服务(SNS)发送消息和/或实施简单的 EC2 和自动缩放操作。
不幸的是，警报还不能直接发送给 Lambda 函数。

警报状态可以显示在 CloudWatch 仪表盘上。

### 云观察事件

CloudWatch Events 服务监听 AWS 资源的状态变化，并创建路由到目标进行处理的事件流。事件用于主动向目标通知状态变化，而无需求助于轮询。示例事件包括启动或终止 EC2 实例、自动缩放操作或 RDS 故障转移。示例目标包括 Lambda 函数和 SNS 主题。

传入的状态更改由将更改签名与事件模式相匹配的规则进行筛选。过滤后，事件作为事件流被路由到指定的目标。

CloudWatch 事件也可以生成计划事件。这对于运行目标很有用，比如根据时间表运行 Lambda 函数。

以下收件人可以用作事件流的目标:

*   EC2 行动
*   λ函数和阶跃函数状态机
*   Kinesis 数据流
*   CloudWatch 日志
*   ECS 任务
*   系统经理运行命令和自动化
*   AWS 批处理作业
*   代码管道中的管道
*   代码构建项目
*   亚马逊检查员评估模板
*   社交网络话题和 SQS 队列
*   另一个 AWS 帐户的事件总线

## CloudTrail

由于有人混淆了 CloudTrail 和 CloudWatch，所以简单介绍一下 CloudTrail。AWS CloudTrail 服务是一个完全独立的服务，它记录和监控整个 AWS 基础设施中的帐户活动。它有助于治理、合规和审计您的帐户。CloudTrail 提供了您的 AWS 帐户活动的事件历史，并可以将 API 事件发送到 AWS CloudWatch 日志进行捕获和存储。

## 总结

CloudWatch 是 AWS 基础设施管理的基础。它为指标和日志提供了强大的捕获和存储机制。

虽然用于查看和分析的管理工具是基本的，但通过使用第三方工具来增强 CloudWatch，您可以轻松地为您的基础架构创建一个全面的监控和管理平台。

#### 使用 SenseDeep 转换 CloudWatch 日志

在 SenseDeep 开发云应用程序时，我们对标准的 CloudWatch 日志查看器感到失望。我们需要一个快速的日志查看器，支持平滑的无限滚动和更好的日志数据显示。因此，SenseDeep 增加了一个非常棒的 CloudWatch 日志查看器。它透明地将日志事件下载并存储在浏览器应用程序缓存中，以便立即查看和以后查看。它提供了平滑的滚动、实时的尾部和强大的结构化查询。它理解结构化日志数据，以便于显示和查询。

[![](img/abb2920852ee855f858600cdfba78442.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wnEYzrjq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sensedeep.cimg/sensedeep/drag-viewer.png)

欢迎您在:[https://app.sensedeep.io/](https://app.sensedeep.com/)免费试用，或在:[https://www.sensedeep.com/doc/](https://www.sensedeep.com/doc/)了解更多关于 SenseDeep 的信息。

## 参考文献

*   [亚马逊云观察](https://aws.amazon.com/cloudwatch/)
*   [亚马逊云手表功能](https://aws.amazon.com/cloudwatch/features/)