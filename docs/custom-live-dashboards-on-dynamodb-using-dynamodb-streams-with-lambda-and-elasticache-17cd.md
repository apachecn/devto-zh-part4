# DynamoDB 上的定制实时仪表盘——使用带有 Lambda 和 ElastiCache 的 DynamoDB 流

> 原文：<https://dev.to/rocksetcloud/custom-live-dashboards-on-dynamodb-using-dynamodb-streams-with-lambda-and-elasticache-17cd>

许多组织使用实时仪表盘来支持关于实时数据的关键任务决策。有时被称为实时仪表板或运营报告，它们为[运营分析](https://rockset.com/blog/operational-analytics-what-every-software-engineer-should-know/)—大型数据集上的低延迟查询提供了可视化层。在本帖中，我们将重点关注对存储在 [DynamoDB](https://aws.amazon.com/dynamodb/) 中的数据构建实时仪表板，因为我们在 Rockset 发现，这种用例非常常见，并且说明了许多数据操作化的最佳实践。

我们将评估几种在定制的实时仪表板中呈现实时数据的流行方法，如果您有一个 web 开发团队，并且需要 100%控制最终的仪表板 UI，您可以选择这些方法。我们考虑了几种方法，它们都使用 DynamoDB 流，但在如何提供仪表板方面有所不同:

**1。DynamoDB Streams + Lambda + S3**

**2 .DynamoDB 流+ Lambda +用于 Redis 的弹性缓存**

**3。DynamoDB Streams + Rockset**

我们将评估每种方法的设置/维护简易性、数据延迟、查询延迟/并发性和系统可伸缩性，以便您可以根据这些标准中哪些对您的用例最重要来判断哪种方法最适合您。

## 实时仪表盘的技术考虑

构建实时仪表板并不简单，因为任何解决方案都需要支持高并发、低延迟的查询以实现快速加载(或者降低使用率/效率)，以及从数据源进行实时同步以实现低数据延迟(或者导致不正确的操作/错失的机会)。低延迟要求排除了直接对 OLTP 数据库中的数据进行操作，OLTP 数据库针对事务性查询而非分析性查询进行了优化。低数据延迟需求排除了基于 ETL 的解决方案，这些解决方案会将数据延迟增加到实时阈值以上，并且不可避免地导致“ETL 地狱”。

DynamoDB 是由 AWS 提供的完全托管的 NoSQL 数据库，它使用分区键针对点查找和小范围扫描进行了优化。虽然对于这些用例来说，DynamoDB 具有很高的性能，但是对于分析查询来说， [DynamoDB 并不是一个很好的选择，因为分析查询通常涉及大范围的扫描和复杂的操作，比如分组和聚合。AWS 知道这一点，并通过创建](https://aws.amazon.com/blogs/database/dynamodb-streams-use-cases-and-design-patterns/) [DynamoDB 流](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)回应了客户的请求，这是一个更改数据捕获系统，可用于通知其他服务 DynamoDB 中新的/修改的数据。在我们的例子中，我们将利用 DynamoDB 流将 DynamoDB 表与更适合服务于分析查询的其他存储系统同步。

## 亚马逊 S3

[![dynamodb lambda s3 static-hosting architecture](img/72d7a24e1f7d54584600b2272481f962.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MPCLwqvq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/5ZcoOX41SWWPDJJDzVpBfE/eb2ae1d1c6ebbbd3a2176bec219dbf47/dynamodb_lambda_s3_static-hosting_architecture.png)

我们将考虑的 DynamoDB 报告和仪表板的第一种方法利用亚马逊 S3 的静态网站托管。在这个场景中，对 DynamoDB 表的更改将触发对 Lambda 函数的调用，该函数将接受这些更改并更新也存储在 DynamoDB 中的一个单独的聚合表。Lambda 将使用 DynamoDB Streams API 高效地遍历最近对表的更改，而不必进行完整的扫描。聚合表的前面是一个在 S3 的静态文件，任何人都可以通过访问 S3 水桶托管网站的 DNS 端点来查看。

例如，假设我们正在组织一场慈善筹款活动，并希望在活动中有一个实时仪表盘来显示我们筹款目标的进展情况。用于跟踪捐赠的 DynamoDB 表可能如下所示

[![example dynamodb table](img/577f6ad57741da49455510041370c938.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FBLJFDD4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/1PemRc7t3zQiX4mbVZtFZB/1cec692c7c45586d34d5d59b420dd81e/example_dynamodb_table.png)

在这种情况下，跟踪每个平台的捐赠以及到目前为止的捐赠总额是合理的。为了存储这些聚合数据，可以使用另一个 DynamoDB 表，如下所示

[![example dynamodb aggregates table](img/97ce0ed290a43e1ccc90bab78d49861d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S6ysBIc3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/6iNwATj02M5CGIs499gDTp/7098b5b8f849172d6d79a60eeb3bbaf4/example_dynamodb_aggregates_table.png)

如果我们在整个募捐过程中让我们的志愿者随时了解这些数字，他们就可以重新安排他们的时间和精力，以最大限度地增加捐款(例如，通过分配更多的人使用电话，因为电话捐款大约是脸书捐款的 3 倍)。

为此，我们将使用 dynamodb-process-stream 蓝图创建一个 Lambda 函数，函数体的形式为

```
exports.handler = async (event, context) => {
  for (const record of event.Records) {
    let platform = record.dynamodb['NewImage']['platform']['S'];
    let amount = record.dynamodb['NewImage']['amount']['N'];
    updatePlatformTotal(platform, amount);
    updatePlatformTotal("ALL", amount);
  }
  return `Successfully processed ${event.Records.length} records.`;
}; 
```

Enter fullscreen mode Exit fullscreen mode

函数 updatePlatformTotal 将从 DonationAggregates 中读取当前聚合(如果不存在，则将它们初始化为 0)，然后更新并写回新值。有两种方法可以更新最终仪表板:

1.  每次 Lambda 被触发时，向 S3 写一个新的静态文件，覆盖 HTML 以反映最新的值。对于不经常变化的可视化数据来说，这是完全可以接受的。
2.  让 S3 的静态文件实际上从 DonationAggregates DynamoDB 表中读取(这可以通过 AWS javascript SDK 来完成)。如果数据经常更新，这是更可取的，因为这将节省许多重复写入 S3 文件。

最后，我们将转到 DynamoDB 流仪表板，并将这个 lambda 函数与捐款表上的 DynamoDB 流相关联。

优点:

*   无服务器/快速安装
*   λ导致低数据延迟
*   如果聚集表比较小，查询延迟会比较好
*   S3 服务的可扩展性

缺点:

*   仪表板中没有特别的查询、优化或探索(它是静态的)
*   最终的聚集仍然存储在 DynamoDB 中，所以如果你有足够多的聚集，你会遇到范围扫描等同样的减速。
*   很难将其应用于现有的大型 DynamoDB 表
*   需要在 DynamoDB 表上提供足够的读/写容量(更多 devops)
*   需要事先确定所有终端指标

TLDR:

*   这是在简单的仪表板上快速显示一些简单指标的好方法
*   您需要在 DynamoDB 中维护一个单独的聚合表，并使用 Lambdas 进行更新
*   由于数据是预先计算的，这些类型的仪表板不会是交互式的

关于这种方法的完整教程，请查看 AWS 博客。

## 缰绳的弹性套

[![dynamodb lambda elasticache-redis architecture](img/8322c74a10998d917af887ca36f5b1fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YY5fqA1y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/6HTgLRmYuTUpglKjAjphM2/7042799209ade09c3d8259e66c822ef9/dynamodb_lambda_elasticache-redis_architecture.png)

我们在 DynamoDB 之上的实时仪表板的下一个选项涉及 Redis 的[elastic cache](https://aws.amazon.com/elasticache/redis/)，这是由 AWS 提供的完全托管的 Redis 服务。Redis 是内存中的键值存储，经常用作缓存。这里，我们将为 Redis 使用 ElastiCache，就像上面的聚合表一样。同样，我们将设置一个 Lambda 函数，每次对 DynamoDB 表进行更改时都会触发该函数，该函数将使用 DynamoDB Streams API 有效地检索最近对表的更改，而无需执行完整的表扫描。然而这一次，Lambda 函数将调用我们的 Redis 服务来更新内存中的数据结构，我们使用这些数据结构来跟踪我们的聚合。然后，我们将利用 Redis 的内置[发布-订阅功能](https://redis.io/topics/pubsub)，在新数据到来时向我们的 webapp 发出实时通知，这样我们就可以相应地更新我们的实时仪表板。

继续我们的慈善筹款示例，让我们使用 Redis 散列来跟踪总数。在 Redis 中，散列数据结构类似于 Python 字典、Javascript 对象或 Java HashMap。首先，我们将在 ElastiCache 中为 Redis [仪表板](https://us-west-2.console.aws.amazon.com/elasticache/home?region=us-west-2#redis:)创建一个新的 Redis 实例。

[![elasticache-redis dashboard](img/785e18a942821e43d12511abd8732557.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A2iXV0mN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/6rMCV1dE9SKpRFJQecl60N/07623884f54ec4c04be5dd40b747c048/elasticache-redis_dashboard.png)

然后，一旦它启动并运行，我们可以使用上面的相同 lambda 定义，只需将 updatePlatformTotal 的实现更改为类似于
的内容

```
function udpatePlatformTotal(platform, amount) {
  let redis = require("redis"),
  let client = redis.createClient(...);

  let countKey = [platform, "count"].join(':')
  let amtKey = [platform, "amount"].join(':')

  client.hincrby(countKey, 1)
  client.publish("aggregates", countKey, 1)
  client.hincrby(amtKey, amount)
  client.publish("aggregates", amtKey, amount)
} 
```

Enter fullscreen mode Exit fullscreen mode

在捐赠记录
的例子中

```
{
  "email": "a@test.com",
  "donatedAt": "2019-08-07T07:26:56",
  "platform": "Facebook",
  "amount": 10
} 
```

Enter fullscreen mode Exit fullscreen mode

这将导致等效的 Redis 命令

```
HINCRBY("Facebook:count", 1)
PUBLISH("aggregates", "Facebook:count", 1)
HINCRBY("Facebook:amount", 10)
PUBLISH("aggregates", "Facebook:amount", 10) 
```

Enter fullscreen mode Exit fullscreen mode

increment 调用将捐赠信息保存到 Redis 服务，publish 命令通过 Redis 的发布-订阅机制将实时通知发送到之前订阅了“聚合”主题的相应 webapp。使用这种通信机制可以确保仪表板可以实时更新，并且只要 Redis 客户机可以订阅，就可以灵活地选择使用哪种 web 框架。

*注意:*您可以随时使用自己的 Redis 实例或其他管理版本，而不是 Amazon ElastiCache for Redis，所有概念都是相同的。

优点:

*   无服务器/快速安装
*   发布-订阅导致低数据延迟
*   Redis 的查找速度非常快→低查询延迟
*   灵活选择前端，因为 Redis 客户端有多种语言版本

缺点:

*   需要另一个 AWS 服务或设置/管理您自己的 Redis 部署
*   需要在 Lambda 中执行 ETL，随着 DynamoDB 模式的改变，这将变得很脆弱
*   难以与现有的大型生产 DynamoDB 表合并(仅流更新)
*   Redis 不支持复杂的查询，只支持查找预先计算的值(没有特别的查询/探索)

TLDR:

*   如果您的仪表板主要依赖于预先计算的值的查找，并且不需要复杂的查询或连接，那么这是一个可行的选择
*   这种方法使用 Redis 存储聚合值，并使用 Redis 发布-订阅将更新发布到您的仪表板
*   比静态 S3 托管更强大，但仍受到预先计算的指标的限制，因此仪表板不会是交互式的
*   所有组件都是无服务器的(如果你使用 Amazon ElastiCache ),所以部署/维护很容易
*   需要开发自己的支持 Redis 订阅语义的 webapp

关于这种方法的深入教程，请查看 AWS 博客。这里的重点是一个通用的 Kinesis 流作为输入，但你可以使用 [DynamoDB Streams Kinesis 适配器](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.KCLAdapter.html)与你的 DynamoDB 表，然后按照他们的教程从那里开始。

## 岩石集

[![dynamodb rockset architecture](img/ef9ad971d09ecd1ab005406e0ce2c60b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B_vaei6W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/10XjYeXMwYP9hNZki0tzPS/bef567dc8874b6897a2c12e212aa9e81/dynamodb_rockset_architecture.png)

在这篇文章中，我们将考虑的最后一个选项是 [Rockset](http://www.rockset.com/) ，一个无服务器的搜索和分析服务。Rockset 的数据引擎有[强动态类型](https://rockset.com/blog/dynamic-typing-in-sql/)和[智能模式](https://rockset.com/blog/from-schemaless-ingest-to-smart-schema-enabling-sql-on-raw-data/)，它们推断字段类型以及它们如何随时间变化。这些属性使得处理 NoSQL 数据(如来自 DynamoDB 的数据)变得简单明了。

在[www.rockset.com](http://www.rockset.com/)创建账户后，我们将使用控制台设置我们的第一个集成——一组用于访问我们数据的凭证。因为我们使用 DynamoDB 作为我们的数据源，所以我们将为 Rockset 提供一个 AWS 访问密钥和秘密密钥对，该密钥对具有适当的权限范围，可以读取我们想要的 DynamoDB 表。接下来，我们将创建一个集合——相当于 DynamoDB/SQL 表——并指定它应该从 DynamoDB 表中提取数据，并使用我们刚刚创建的集成进行身份验证。控制台中的预览窗口将从 DynamoDB 表中提取一些记录并显示出来，以确保一切正常，然后我们就可以按“Create”了。

[![rockset console create-collection 1](img/947834101b9fb1615c93c4e80124a7a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--toYipUA3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/1wYxmbwCEJEY92LbsoNgbf/430ca5bdb8ce5c542f94073c0bad3c29/rockset_console_create-collection_1.png) 

[![rockset console create-collection 2](img/71d73fd2ebfc11db49f5c0f4994ac1f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LfGxLu3C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/3Br4aZYqHsftN7eGX3TMb/87822e3e36430ef65d1258920ebef542/rockset_console_create-collection_2.png)

不久之后，我们可以在控制台中看到集合被创建，数据从 DynamoDB 流入。我们可以使用控制台的查询编辑器来试验/调整将在我们的实时仪表板中使用的 SQL 查询。由于 Rockset 有自己的查询编译器/执行引擎，所以有对数组、对象和嵌套数据结构的一流支持。

[![rockset console query-editor](img/70b0f6aa76f03ac6c563432014295f42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8lBG4Yfa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/7v0pWRkfc8PxBIygSBnEd9/7491966d8eba31761cbb12fa1ea70e4f/rockset_console_query-editor.png)

接下来，我们可以在控制台中创建一个 API 密钥，仪表板将使用它对 Rockset 的服务器进行身份验证。我们可以将查询从控制台查询编辑器导出到各种语言的可用代码片段中。Rockset 支持 SQL over REST，这意味着任何编程语言的任何 http 框架都可以用来查询数据，并且为了方便起见，还提供了几个客户端库。

[![rockset console export-query](img/62be057342c12aa77cb48289f2391150.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ce-tXFYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/4pL4AL7yy8g7X6weUXVros/37d53991e497b5e5c666e959f9ba1aed/rockset_console_export-query.png)

剩下的就是在我们的仪表板中运行我们的查询。Rockset 的云原生架构允许它根据需要动态扩展查询性能和并发性，即使在包含复杂嵌套数据且类型不一致的大型数据集上也能实现快速查询。

优点:

*   无服务器–快速设置，无代码 DynamoDB 集成，无需配置/管理
*   专为开箱即用的低查询延迟和高并发性而设计
*   与 DynamoDB(和其他源)实时集成，实现低数据延迟，无需维护管道
*   强动态类型和智能模式可以处理混合类型，并且可以很好地与 NoSQL 系统(如 DynamoDB)配合使用
*   与各种定制仪表板(通过客户端 SDK、JDBC 驱动程序和 SQL over REST)和 BI 工具(如果需要)集成

缺点:

*   针对活动数据集而非归档数据进行了优化，最佳点最高可达数十 TB
*   不是事务数据库
*   这是一项对外服务

TLDR:

*   如果您对实时仪表板中的最新数据有严格要求，需要支持大量用户，或者希望避免管理复杂的数据管道，请考虑这种方法
*   从 DynamoDB(和许多其他来源)到实时仪表板的内置集成
*   可以处理混合类型、同步现有表和许多低延迟查询
*   最适合从几个 GB 到几十个 TB 的数据集

有关如何将 Rockset 与 DynamoDB 集成的更多资源，请查看这篇博文中的[,这篇博文讲述了一个更复杂的例子。](https://rockset.com/blog/running-fast-sql-on-dynamodb-tables)

## 结论

我们已经介绍了几种在 DynamoDB 数据上构建定制的实时仪表板的方法，每种方法都有自己的优缺点。希望这可以帮助您评估适合您的用例的最佳方法，这样您就可以更进一步操作您自己的数据了！