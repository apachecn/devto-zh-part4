# AWS 迁移:要使用的 3 个主要成本优化策略

> 原文：<https://dev.to/serhiykozlov/aws-migration-3-major-cost-optimization-strategies-to-use-4hcn>

## 使用成熟的成本优化策略，最大限度地降低云迁移到 AWS 的成本。让您的企业在预算范围内实现云计算！

当他们的内部系统开始带来更多挑战而不是带来好处时，大多数企业开始考虑云迁移。这些挑战大多表现为在添加硬件、更新软件、减少潜在灾难或防止违规方面的重大投资。AWS 成本优化模型是企业的理想解决方案。

就这些障碍而言，云计算的[优势是众所周知的:](https://www.romexsoft.com/blog/migration-to-aws-cloud-benefits/)

*   降低基础设施的总成本
*   更少的停机时间/更高的 SLA 级别
*   升级/降级的即时可扩展性和容量
*   [业务连续性和更快的灾难恢复](https://www.romexsoft.com/blog/disaster-recovery-plan/)
*   增强的安全性

更重要的是，即使是预算紧张的小型企业也可以获得与行业巨头相同的世界级云解决方案，包括云存储、计算、管理等，并且完全可以“按需付费”这将使您的企业获得前所未有的灵活性，并能够在上市时间和服务水平方面与更大的竞争对手竞争。

唯一的问题变成了使用哪家云平台提供商。虽然有许多进入云服务业务的公司，但三个主要竞争者是谷歌云平台(GCP)、微软 Azure 和亚马逊网络服务(AWS)。这三者中，AWS 是目前三者中最强大的。

### 为什么选择 AWS 成本优化模型？

AWS 被认为是云服务公司的“旗舰”。自 2004 年推出这项服务以来，它一直在增加新的功能和服务，并且仍然是最全面的产品(超过 100 种)。这些好处包括:

*   弹性计算云(EC2)–按需扩展计算
*   选择信号源的灵活性
*   可伸缩性——能够快速改变资源，并且仅在需要时才使用这些资源
*   C3:只为你使用的存储付费
*   无停机备份的红移解决方案
*   最新的安全措施，包括防止自然灾害
*   自动备份，快速轻松地设置。
*   公司 IT 支持全天候
*   无服务器解决方案(Lambda)-仅在应用程序实际运行时支付
*   code build–使 IT 开发人员能够自动构建服务器。AWS 可以在构建过程中运行单元测试。

当然，这里有太多的好处可以列出来，但是在其他云平台(Google 和 Azure)赶上来之前，AWS 是最全面的解决方案，特别是如果你想保持 TOC 下降的话。

### 如何优化 AWS 成本

最具成本效益地使用 AWS 的关键在于负责任地使用您选择的服务。例如，对于短期负载，您可能需要扩展到 1000 个实例(服务器),这将花费很多。但是，如果您没有一个适当的流程来在低使用率期间缩减这些实例，您就是在为不需要的服务付费。

从节约使用 AWS 的角度来看，最好的起点之一就是 AWS 本身。该公司已经开发并不断更新了一个名为[良好架构框架](https://aws.amazon.com/architecture/well-architected/)的文档，这是一个最佳云计算最佳实践的汇编，其中包括数百个节省成本的技巧和策略。

下面，您将看到基于这些最佳实践的三种全面的成本优化策略，以及我们作为[托管 AWS 支持合作伙伴](https://www.romexsoft.com/aws-managed-services/)的丰富经验。

### 1.关闭不使用的内容

这是可避免成本的首要原因。构建大量基础设施既快速又容易，而且开发者也很容易忘记他们已经构建好的一切。成本也在一点点减少。

找到并清除所有这些无用的东西，甚至更好的是，从一开始就阻止它们的积累是理想的。幸运的是，AWS 有很多工具可以做到这一点。以下是对你的价格标签影响最大的几个:

*   **根据自定义标签分解账单**:对于每一个项目，开发人员必须创建一个“project id=1234”标签，这个标签应该附加到项目的每一个资源上。像[涂鸦猴](http://answersforaws.com/code/graffiti-monkey/)或[看门猴](https://medium.com/netflix-techblog/janitor-monkey-keeping-the-cloud-tidy-and-clean-d517ad74d648)这样的工具将允许自动标记和删除没有正确标记的资源。然后，使用 IAM 服务，某些工作人员可以查看发票，甚至在一些账单超过之前定义的阈值时收到警报。
*   S3 生命周期 : S3 基本上是一个文件的垃圾场。它继续扩展，允许添加几乎无限数量的文件。使用 AWS [生命周期策略](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html)，您可以管理您的“一桶”文件以节省成本。您可能将日志记录到一个只在特定时间段需要的存储桶中。之后它们需要被删除；其他文件可能会在一段时间内被频繁访问，但之后不会被频繁访问。您可能希望将它们存档一段时间，然后删除它们。您可能希望从一开始就归档一些文件(例如数据库备份)，以满足法规遵从性等要求。有了生命周期配置，AWS 可以被告知在哪里以及如何将文件转移到更便宜的存储中，对它们进行归档，或者完全删除它们。AWS [设置铲斗生命周期配置](https://docs.aws.amazon.com/AmazonS3/latest/dev/how-to-set-lifecycle-configuration-intro.html)中解释了实现所有这些的方法。如果你不这样做，你最终会为你不再需要的垃圾付费。
*   **自动缩放**:一些 AWS 服务(例如 EC2 和 DynamoDB)允许您设置自动缩放。因此，开发团队根据预定义的指标来定义容量何时应该扩大或缩小。因此，在不需要用户干预的情况下，成本是根据该比例计算的。
*   **优化大数据** : Redshift 为大数据提供了一个巨大的数据仓库，以便可以操作和分析这些数据(当然也包括备份)。但是一旦数据的有用性被推断出来，就没有必要再保留它了。把它移到冷藏库以获得更便宜的价格。注意:不要忘记设置适当的公共云配置，以保护您存储在云中的所有重要数据。

### 2.正确使用 AWS 工具

AWS 提供的工具数量之多可能会令人望而生畏。通常，简单的解决方案(例如 EC2)并不是最便宜的。寻找替代品会有回报。这里有几个例子:

*   **负载平衡**:在 AWS 存在之前，有任意数量的开源负载平衡工具，比如 Apache。IT 团队需要明白，他们仍然可以使用这些工具，只需将它们安装在 EC2 上。考虑一下:如果您的负载被认为是中等负载，那么在一个月的时间内，您可能仍然需要多达 20 个实例。你可以用这种方法积累一大笔账单。另一方面，AWS ELB 服务只是成本的一小部分，其自动功能包括扩展、修补和监控/警报。换句话说，如果有一个托管解决方案，在 EC2 中构建它是不划算的。
*   **EC2 与 Lambda** : Lambda 可能是最流行的 AWS 工具之一，主要是因为它的成本比 EC2 低得多。如果开发者有可以在 AWS 上运行的定制代码，Lambda 可以提供更便宜的解决方案。事实上，EC2 的使用正在减少，现在主要用于安装独立的应用程序，比如 WordPress。
*   **使用“预约”**:大部分 AWS 云计算解决方案都允许客户通过“预约”来支付。预订是一种很好的预订使用的方法，以很高的折扣率提前付款。因此，如果您知道在接下来的几年中您将需要某个类型的 X 个实例，那么您可以现在购买它们并节省一大笔钱。
*   **带宽成本**:通过 AWS 获得的带宽成本不一定比您在内部支付的成本更低。但是重要的是要做研究以尽可能降低成本。费用将因您使用的 AWS 服务而异。例如，红移有相对昂贵的带宽成本，而 ELB 有一些最便宜的。AWS 确实有帮助降低成本的工具，包括[直连](https://aws.amazon.com/directconnect/?sc_channel=PS&sc_campaign=acquisition_US&sc_publisher=google&sc_medium=direct_connect_b&sc_content=aws_direct_connect_e&sc_detail=direct%20connect%20aws&sc_category=direct_connect&sc_segment=194354083609&sc_matchtype=e&sc_country=US&s_kwcid=AL!4422!3!194354083609!e!!g!!direct%20connect%20aws&ef_id=VahAXwAABAXtBC4r:20180413204640:s)、 [CloudFront 地理围栏](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/georestrictions.html)和[雪球](https://aws.amazon.com/snowball)。

### 3.规划要现实

虽然规划有时可能是“组织生存的祸根”，但使用 AWS 云服务时情况并非如此。

更多阅读:[应用迁移最佳实践](https://www.romexsoft.com/blog/application-migration-best-practices/)

因为 AWS 非常注重自助服务，所以组织必须建立并实施标准和计划。而且企业越大，这一点越重要。必须有成本控制和相对于成本的完全可见性。这里有一些“必做之事”

*   **建立全公司的标记拓扑**。这些是可以与每个资源相关联的简单元数据。标记的关键是你将能够分离出成本，并看到哪里的成本可能比它们应该的要多。然后你可以开始分析，看看如何削减成本。
*   **部署计划**。AWS 最大的好处之一就是你有这么多的选择来做同样的事情。如果您要确定成本以及超出了哪些成本阈值，每个人都必须保持一致。您可以使用 Jenkins 之类的工具进行部署，使用 Chef、Puppet 或 Ansible 进行维护。
*   **备份和数据恢复**:任何计划都必须包括测试用的备份和恢复模拟。这一直都是现场完成的，也必须用 AWS 来完成。AWS 基础设施变化很快，因此应定期审查恢复计划。更换备份和恢复选项可能会节省成本。

我们的 AWS 案例研究:

### 我们能帮上什么忙

如你所见，通过使用 AWS 优化成本是一项具有挑战性的任务。它需要对服务、新选项进行一致的审查，并在 [AWS 专业服务](https://www.romexsoft.com/aws-cloud-services/)和工具中进行成本比较。事实上，一个小团队可以花大部分时间独自完成这项任务。

更多阅读: [AWS:安全流程概述白皮书](https://www.romexsoft.com/blog/aws-overview-of-security-processes-whitepaper/)，[十大 AWS 云迁移工具](https://www.romexsoft.com/blog/top-10-aws-cloud-migration-tools/)

Romexsoft 旨在对您的云基础架构进行深度审计，并向您展示可以实现成本节约的地方。我们根据您的需求和预算提供定制解决方案——标记拓扑、计费警报、监控和调整所有战略、架构和计划、全面备份和恢复测试，以及 24/7 [AWS 技术支持服务](https://www.romexsoft.com/noc-technical-support/)。[立即联系](https://www.romexsoft.com/contact-us/)了解我们如何降低您的 AWS 账单！

由 Romexsoft 撰写于 2019 年 4 月 23 日
原载于 Romexsoft 博客[《AWS 迁移:要使用的 3 大成本优化策略》](https://www.romexsoft.com/blog/aws-migration-3-major-cost-optimization-strategies-to-use/)