# 亚马逊 DynamoDB 的 3 个成本削减技巧

> 原文：<https://dev.to/rocksetcloud/3-cost-cutting-tips-for-amazon-dynamodb-2f0j>

*作者为[阿尼鲁德·拉曼森](https://www.linkedin.com/in/anirudhrx/)T3】*

Amazon DynamoDB 是 AWS 云中的一个托管 NoSQL 数据库，它为从移动应用后端到广告技术的各种用例提供了一个关键的基础设施。DynamoDB 针对需要读写单个键但不需要连接或其他 RDBMS 特性的事务性应用程序进行了优化。对于这部分需求，DynamoDB 提供了一种几乎可以无限扩展的数据存储方式，只需要最少的维护。

虽然 DynamoDB 非常流行，但我们经常从开发人员那里听到的一个常见抱怨是 DynamoDB 很贵。特别是，随着使用量以几乎令人惊讶的方式增长，成本会急剧增加。在这篇文章中，我们将探讨 DynamoDB 被认为大规模昂贵的三个原因，并概述使 DynamoDB 成本更合理的步骤。

**DynamoDB 分区键**

鉴于使用 DynamoDB 的简单性，开发人员可以在短时间内走得很远。但是，在开始使用数据分布之前，如果不仔细考虑它，就会有一些潜在的陷阱。为了有效地管理 DynamoDB 中的数据，了解 DynamoDB 的一些内部知识——了解*数据是如何存储的——非常重要。*

正如我们之前提到的，DynamoDB 是一个 NoSQL 数据存储，这意味着它有效支持的操作是 GET(通过主键或索引)和 PUT。存储在 DynamoDB 中的每条记录都被称为一个项，这些项存储在分区中。这些分区都是自动管理的，不向用户公开。每个项目都有一个分区键，用作内部哈希函数的输入，以确定项目将位于哪个分区中。分区本身存储在 SSD 上，并在一个区域中的多个可用性区域之间复制。

每个单独的分区都有一些限制:

*   单个分区最多可以存储 10 GB 的数据。
*   单个分区最多可以支持 3000 个读容量单元(rcu)或 1000 个写容量单元(wcu)。

考虑到这些限制，我们知道可以根据两个标准将数据放在更多的分区上。如果单个分区的大小超过 10 GB，则需要创建新的分区来存储更多数据。类似地，如果用户请求的读容量或写容量增长超过了单个分区支持的范围，将会在后台创建新的分区。

除了分区之外，另一个值得了解的方面是 DynamoDB 中读写是如何定价的。读取和写入消耗称为 rcu(读取计算单元)和 wcu(写入计算单元)的抽象单元。DynamoDB 中的每次读取或写入都会消耗这些单元，因此，随着读取和写入工作负载的增长，您将分别消耗更多的 rcu 和 wcu。

我们选择的分区键决定了数据在分区之间的平均分布程度。选择一个不是非常随机的分区键是一种反模式，会导致这些分区中的数据分布不均匀。直到最近，RCU 和 WCU 在分区间的分配还是无弹性的，是静态的。然而，在由于数据的不均匀分布而导致的“热键”的情况下，一些分区将比其他分区需要更多的 RCU 和 WCU 分配，并且这导致过度供应 rcu 和 wcu 以确保过载的分区具有足够的 rcu 和 wcu 的问题。

2018 年，亚马逊推出了[Amazon dynamo db adaptive capacity](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html#bp-partition-key-partitions-adaptive)，通过允许分区之间 rcu 和 wcu 的分配更加动态，缓解了这一问题。今天，DynamoDB 甚至做这种再分配[【即刻】](https://aws.amazon.com/about-aws/whats-new/2019/05/amazon-dynamodb-adaptive-capacity-is-now-instant/)。因此，即使存在热点关键问题，也可能没有立即过度供应远远超过所需的区域协调单位和区域协调单位的需求。

但是，如果您还记得单个分区上的 wcu 和 rcu 的限制以及整体大小限制，如果您希望分配超过这些限制的资源(某些高流量应用程序就是这种情况)，您可能会遇到高成本问题。[耐克关于 DynamoDB 成本的工程博客](https://medium.com/nikeengineering/reducing-dynamodb-costs-in-aws-5047cbf726c9)提到这是他们设置的成本驱动因素之一。有趣的是，他们没有重新设计分区键，而是选择将一些表移动到关系数据存储中。

简而言之，以次优方式对数据进行分区是 DynamoDB 成本增加的一个原因。尽管自适应能力在一定程度上缓解了这一问题，但最好还是设计具有足够随机的分区键的 DynamoDB 表，以避免热分区和热键的问题。

**DynamoDB 读/写容量模式**

在为您的表提供 rcu 和 wcu 时，DynamoDB 有几个不同的模式可供选择。选择正确的模式会对您的应用程序性能和成本产生重大影响。

在顶层，有两种模式:调配容量和按需容量。在调配的容量内，您可以获得保留价格，类似于 AWS 中其他地方的保留实例的工作方式，在 AWS 中，您可以通过在一段时间内向产品投入一定量的费用来获得折扣价格。然后是 DynamoDB 自动扩展，它可以与调配容量模式结合使用。

您应该使用的模式取决于您希望在 DynamoDB 上构建的应用程序的类型。供应能力模式是指您支付一定数量的 rcu 和 wcu，并且它们随时可供您使用。在以下情况下，这是推荐的操作模式:

*   如果您有一个稳定的工作负载，在 RCU 和 WCU 表现出相似的要求，并且变化很小。
*   与 DynamoDB 自动缩放结合使用，如果您的工作负载表现出可预测的可变性，例如根据一天中的时间。
*   如果服务的读/写限制成本非常高。

如果您遇到突然的峰值或突发工作负载，这可能会非常昂贵，因为您调配的容量需要超过峰值才能避免节流。当应用程序的容量消耗逐渐增加或减少时，自动伸缩会有所帮助，但它通常无法应对峰值和突发事件。

如果您选择使用自动伸缩，随着容量的调整，一些请求可能会受到限制，这在操作像电子商务网站这样的面向客户的应用程序时可能是不可接受的，因为这会对您的收入产生影响。如果我们选择提供比任何突发/峰值所需容量更多的固定容量，这将确保您的用户获得最佳体验。但这也可能意味着大量时间浪费了大量容量。

当您开始处理一个新的工作负载，并且没有对其进行容量估计时，或者当使用情况不可预测时，切换到按需模式可能是一个很好的节省成本的措施。在按需模式下，DynamoDB 管理所有容量，并完全自行扩展。一些用户报告称[通过从供应模式转向按需模式](https://blog.koan.co/how-on-demand-pricing-slashed-our-dynamodb-bill-by-90-27857c49f30b)节省了大量成本。

根据 RCU/WCU 的数据，按需模式的成本可能是调配容量的 6 到 7 倍，但它在处理最大和最小负载之间的巨大差异方面做得更好。按需模式对于表的开发实例也很有用，在这种情况下，使用量通常会下降到零，并出现不可预测的峰值。

对于您的特定表，按需模式是否具有成本效益？这取决于您的访问模式、数据规模和业务目标。因此，为您的特定表格选择正确的模式并设置正确的自动缩放非常重要。您的表的最佳模式可以根据用例、工作负载模式和容错能力而有所不同。

**DynamoDB 扫描和 GSI**

DynamoDB 支持两种不同类型的读取操作，即查询和扫描。查询是基于主键或索引键的查找。顾名思义，扫描是扫描整个表以找到特定结果的读调用。DynamoDB 针对的操作是对表中的一个或几个项目进行操作时的查询操作。DynamoDB 还支持辅助索引，这允许基于主键以外的键进行查找。在读取和写入期间，辅助索引也会消耗 rcu 和 wcu。

有时，对 DynamoDB 数据运行更复杂的查询很重要。这可能是为一家电子商务零售商找到某段时间内购买最多的前 10 项商品，或者为一个广告平台找到广告转换率。对于这些类型的查询，扫描通常非常慢，因此第一步通常是创建 GSI(全局二级索引)。

正如耐克发现的那样，过度使用全球二级指标可能代价高昂。Nike 采用的解决方案是将这些工作负载转移到关系数据库中。但是，这并不总是一个选项，因为有些事务性查询在 DynamoDB 上比在关系数据库上运行得更好，而关系数据库可能需要更多的调优。对于复杂查询，尤其是分析查询，通过将 DynamoDB 表与更适合高效运行复杂查询的不同工具或服务同步，可以节省大量成本。

[Rockset](https://rockset.com/) 就是这样一个运营分析引擎，它是云原生的，不需要管理服务器或基础架构。一旦获得了对 DynamoDB 表的读取权限，Rockset 集合就可以通过利用 DynamoDB 流中的 changelogs 来复制 DynamoDB 中发生的更改。这为您提供了 Rockset 中 DynamoDB 表的最新(几秒钟内)索引版本。您可以在这个索引集合上使用 SQL 的全部功能运行复杂的 OLAP 查询，并通过使用 Rockset API 和 SDK 构建实时仪表板或自定义应用程序来服务这些查询。

这种方法比直接在 DynamoDB 上运行这些查询要便宜得多，因为 Rockset 是一个搜索和分析引擎，专门针对半结构化数据进行索引和运行复杂查询。利用[聚合索引](https://rockset.com/blog/converged-indexing-the-secret-sauce-behind-rocksets-fast-queries/)，Rockset 将 SQL 查询转化为在 [RocksDB-Cloud](https://github.com/rockset/rocksdb-cloud) 上的快速键查找。每个查询都能够利用分布式执行和底层索引来确保在几毫秒内返回查询结果。

Rockset 对于希望在其事务性数据存储之上构建可操作的分析仪表板以监控系统当前状态的开发人员来说尤其有用。Rockset 用户通过利用这种实时同步和对 Rockset 的查询来构建[实时仪表盘](https://rockset.com/blog/full-uses-rockset-with-dynamodb-for-live-dashboard/)以及增强[搜索应用](https://rockset.com/blog/case-study-decore-uses-rockset-for-search-and-analytics-on-dynamodb/)。

总之，选择不当的分区键、错误的容量模式、扫描和全局二级索引的过度使用都是导致应用程序规模不断扩大时 DynamoDB 成本飙升的原因。与 DynamoDB 相关的大部分成本往往源于对其内部缺乏了解，或者试图针对其从未被设计为有效服务的用例对其进行改造。明智地选择分区键，选择适合您的工作负载的操作模式，并使用专用的操作分析引擎，可以提高 DynamoDB 表的可伸缩性和性能，同时控制 DynamoDB 的开销。

其他 DynamoDB 资源:

*   [在 DynamoDB 表上运行快速 SQL](https://rockset.com/blog/running-fast-sql-on-dynamodb-tables/)
*   [对 DynamoDB 的分析:比较 Athena、Spark 和 Elastic](https://rockset.com/blog/analytics-on-dynamodb-athena-spark-elastic/)
*   [在 DynamoDB 上定制实时仪表盘——通过 Lambda 和 ElastiCache 使用 DynamoDB 流](https://rockset.com/blog/live-dashboards-dynamodb-streams-lambda-elasticache/)
*   [Tableau 操作仪表板和 DynamoDB 报告——评估红移和 Athena](https://rockset.com/blog/tableau-operational-dashboards-reporting-dynamodb-redshift-athena/)

* * *

最初发布于[信息世界](https://www.infoworld.com/article/3409075/3-cost-cutting-tips-for-amazon-dynamodb.html)。