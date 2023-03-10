# 2019 年开源数据库报告:顶级数据库、公共云和内部部署、多语言持久性

> 原文：<https://dev.to/scalegrid/2019-open-source-database-report-top-databases-public-cloud-vs-on-premise-polyglot-persistence-3fom>

[![2019 Open Source Database Report: Top Databases, Public Cloud vs. On-Premise, Polyglot Persistence](img/77b4c669460aecca07d75c33acb0bedd.png)](https://scalegrid.io/blog/2019-open-source-database-report-top-databases-public-cloud-vs-on-premise-polyglot-persistence/)

准备从商业数据库过渡到开源，想知道 2019 年哪些数据库最受欢迎？想知道内部部署、公共云和混合云基础架构是否最适合您的数据库战略？或者，考虑在您的应用程序中添加一个新的数据库，并想看看哪些组合最受欢迎？在上个月的 Percona Live 活动中，我们找到了您需要的所有答案，并对以下免费趋势报告进行了分析:

*   **[使用的顶级数据库](https://scalegrid.io/blog/2019-open-source-database-report-top-databases-public-cloud-vs-on-premise-polyglot-persistence/#topdbs) :** 开源与商用
*   **[云基础架构分析](https://scalegrid.io/blog/2019-open-source-database-report-top-databases-public-cloud-vs-on-premise-polyglot-persistence/#cloud) :** 公共云与本地云、混合云
*   **[多语言持久性趋势](https://scalegrid.io/blog/2019-open-source-database-report-top-databases-public-cloud-vs-on-premise-polyglot-persistence/#polyglot) :** 使用的数据库数量&顶级组合

## 2019 年使用的顶级数据库

那么，2019 年哪些数据库最受欢迎呢？我们按照开源数据库和商业数据库对数据进行了分类:

#### 开源数据库

开源数据库是免费的社区数据库，其源代码可供公众使用，并且可以修改或用于其原始设计。开源数据库的流行例子包括 MySQL、PostgreSQL 和 MongoDB。

#### 商业数据库

商业数据库是由商业企业开发和维护的，可通过支付许可订阅费来使用，不得修改。商业数据库的常见例子包括 Oracle、SQL Server 和 DB2。

### 顶级开源数据库

MySQL 仍然是排名第一的免费开源数据库，占开源数据库使用量的 30%以上。这并不奇怪，因为根据[的数据库引擎](https://db-engines.com/en/ranking_trend)，MySQL 多年来一直保持着这一地位。

[![2019 Most Popular Open Source Databases Used Report Pie Chart - ScaleGrid](img/21245e321e5ca07fec5d58c6e28cdb40.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Most-Popular-Open-Source-Databases-Used-Report-Pie-Chart-ScaleGrid.png)

PostgreSQL 位居第二，占开源数据库用户的 13.4%，紧随其后的是 [MongoDB](https://scalegrid.io/mongodb.html "MongoDB Hosting") ，占第三名的 12.2%。根据 DB-Engines 趋势流行度排名，这也是可以预期的，但我们在三个月前的 [2019 年数据库趋势 SQL 与 NoSQL，顶级数据库，单个与多个数据库使用](https://scalegrid.io/blog/2019-database-trends-sql-vs-nosql-top-databases-single-vs-multiple-database-use/ "2019 Database Trends – SQL vs. NoSQL, Top Databases, Single vs. Multiple Database Use")报告中看到 MongoDB 以 24.6%的份额排名第二。

虽然超过 50%的开源数据库使用是由前 3 名代表的，但我们也看到了#4 [Redis](https://scalegrid.io/redis.html "Redis Hosting") 、#5 [MariaDB](https://mariadb.org/) 、#6 [Elasticsearch](https://www.elastic.co/) 、#7 [Cassandra](http://cassandra.apache.org/) 和#8 [SQLite](https://www.sqlite.org/index.html) 的良好代表。最后 2%的数据库包括 [Clickhouse](https://clickhouse.yandex/) 、 [Galera](http://galeracluster.com/) 、 [Memcached](https://memcached.org/) 和 [Hbase](https://hbase.apache.org/) 。

### 顶级商业数据库

在下一张图中，我们看到了一份独特的报告，它代表了多语言持久性和迁移趋势:与开源数据库一起使用的顶级商业数据库。

我们已经看到了利用多种数据库类型来满足您的应用程序需求的增长趋势，并希望比较组织如何在单个应用程序中同时使用商业和开源数据库。该报告还代表了正在迁移到开源数据库的商业数据库用户。例如，PostgreSQL 是连续两年受欢迎程度增长最快的数据库，其 11.5%的用户群来自目前正在向 PostgreSQL 迁移的组织(T2)。

那么，现在我们已经解释了这个报告代表了什么，让我们来看看与开源一起使用的顶级商业数据库。

[![2019 Most Popular Commercial Databases Used with Open Source Report Pie Chart - ScaleGrid](img/3cfbf0313a8fab248efbd141e991c503.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Most-Popular-Commercial-Databases-Used-Report-Pie-Chart-ScaleGrid.png)

[Oracle](https://www.oracle.com/index.html) 是世界上排名第一的数据库，代表了超过 2/3 的商业和开源数据库组合。这份报告中令人震惊的是甲骨文和第二名微软 [SQL Server](https://www.microsoft.com/en-us/sql-server/default.aspx) 之间的巨大差距，因为根据 DB-Engines，它保持着小得多的差距。IBM [Db2](https://www.ibm.com/analytics/db2) 排在第三位，占商业数据库结合开源的 11.1%。

## 云基础设施按数据库细分

现在，让我们看一下按数据库管理系统划分的云基础架构设置。

### 公共云与本地云、混合云

我们询问了我们的开源数据库用户他们是如何托管其数据库部署的，以确定本地、公共云和混合云部署之间的当前趋势。

令人惊讶的是，49.5%的开源数据库部署是在内部运行的，排名第一。虽然我们预料到了这一结果，但我们对内部部署的比例感到惊讶。在我们最近的 [2019 PostgreSQL 趋势报告](https://scalegrid.io/blog/2019-postgresql-trends-report-private-vs-public-cloud-migrations-database-combinations-top-reasons-used/ "2019 PostgreSQL Trends Report: Private vs. Public Cloud, Migrations, Database Combinations & Top Reasons Used")中，本地私有云部署占 59.6%，比该报告高出 10%以上。

公共云位居第二，占开源数据库部署的 36.7%，与 PostgreSQL 报告中的 34.8%一致。然而，从该报告来看，混合云增长显著，开源数据库占 13.8%，PostgreSQL 部署占 5.6%。[![2019 Open Source Databases Report: Public Cloud vs Private Cloud vs On-Premise Pie Chart - ScaleGrid](img/293a93ca9282907b63f5d69d784b4a7e.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Open-Source-Databases-Public-Cloud-vs-Private-Cloud-vs-On-Premise-Report-Pie-Chart.png)

那么，哪种云基础架构适合您呢？下面是公共云与内部部署和混合云的快速介绍:

#### 公共云

公共云是一种云计算模式，通过互联网提供 IT 服务。公共云通常通过订阅使用模式购买，非常容易设置，不需要大量前期投资，并且可以随着应用需求的变化快速扩展。

#### 内部部署

内部部署或私有云部署是专用于在自己的数据中心运行的单个组织的云解决方案(或与第三方供应商异地合作)。有更多的机会通过内部设置来定制您的基础架构，但需要在硬件和软件计算资源方面进行大量的前期投资，以及持续的维护责任。这些部署类型最适合具有高级安全需求的组织、受监管的行业或大型组织。

#### 混合云

混合云是公共云和私有云解决方案的混合体，集成到一个基础架构环境中。这允许组织在公共云和私有云之间共享资源，以提高效率、安全性和性能。这些最适合需要内部基础架构的高级安全性以及公共云的灵活性的部署。

现在，让我们来看看每种开源数据库类型最受欢迎的云基础设施。

### 开源数据库部署:内部部署

在此图以及下面的公共云和混合云图中，我们按照利用这种类型的云基础架构的部署百分比来细分每个单独的开源数据库。

那么，哪些开源数据库最常部署在内部呢？PostgreSQL 以 55.8%的本地部署率位居第一，紧随其后的是 MongoDB(52.2%)、Cassandra(51.9%)和 MySQL(50%)。[![2019 Percent of Open Source Databases Using an On-Premise Infrastructure Report - ScaleGrid](img/6911af06554892f9c3dfc66bc86a4978.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Open-Source-Databases-Using-On-Premise-Infrastructure-Report-Chart.png)

报告不到一半内部部署的开源数据库包括 Maria db(47.2%)、SQLite(43.8%)和 Redis(42.9%)。最少在内部部署的数据库是 Elasticsearch，仅占 34.5%。

### 开源数据库部署:公共云

现在，让我们看看公共云中开源数据库的分类。

SQLite 是公共云基础架构中部署最频繁的开源数据库，占其部署的 43.8%，紧随其后的是 Redis，占 42.9%。MariaDB 公共云部署率为 38.9%，MySQL 为 36.7%，Elasticsearch 为 34.5%。

[![2019 Percent of Open Source Databases Using a Public Cloud Infrastructure Report - ScaleGrid](img/ef168ebeeb7cf9dd65f14a81db54fca4.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Open-Source-Databases-Using-Public-Cloud-Infrastructure-Report-Chart.png)

三个数据库在公共云中的部署不到 1/3，包括 MongoDB 的 30.4%，PostgreSQL 的 27.9%，以及公共云部署最少的 Cassandra 的 25.9%。

### 开源数据库部署:混合云

现在，我们已经知道了开源数据库如何在本地云与公共云之间进行划分，让我们来看看利用这两种计算环境的部署。

利用混合云的排名第一的开源数据库是 Elasticsearch，占 31%。最接近混合云的数据库是 Cassandra，仅占 22.2%。

[![2019 Percent of Open Source Databases Using a Hybrid Cloud Infrastructure Report - ScaleGrid](img/03f2ff5d5679815895bc9e4ff38b4eb9.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Open-Source-Databases-Using-Hybrid-Cloud-Infrastructure-Report-Chart.png)

MongoDB 在混合云中的部署比例排名第三，为 17.4%，然后是 PostgreSQL，为 16.3%，Redis 为 14.3%，MariaDB 为 13.9%，MySQL 为 13.3%，最后是 SQLite，仅占混合云中部署的 12.5%。

### 开源数据库部署:多云

平均而言，20%的公共云和混合云部署正在利用多云战略。多云是使用两种或两种以上的云计算服务。我们还查看了使用的云数量，发现一些部署在一个组织内利用了多达 5 个不同的云提供商:

[![Average Number of Clouds Used for Open Source Database Multi-Cloud Deployments - ScaleGrid Report](img/36e6bf9d05dcfd03823f3b85de2264b1.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/Average-Number-of-Clouds-Used-for-Open-Source-Database-Multi-Cloud-Deployments-ScaleGrid-Report.png)

### 最受欢迎的开源数据库托管云提供商

在我们上次对云基础设施的分析中，我们分析了哪些云提供商最受开源数据库托管的欢迎: [![2019 Most Popular Cloud Providers for Open Source Database Hosting Pie Chart - ScaleGrid](img/7d76845f9cfddb1032318d1c69bcbb5a.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/2019-Most-Popular-Cloud-Providers-for-Database-Hosting-Pie-Chart.png)

AWS 是开源数据库托管的头号云提供商，在本次调查中占所有云部署的 56.9%。谷歌云平台(GCP)以 26.2%位居第二，出人意料地领先 Azure 的 10.8%。Rackspace 紧随其后，占部署的 3.1%，DigitalOcean 和 Softlayer 紧随其后，占云中开源部署的剩余 3%。

## 多语言持久性趋势

[多语言持久性](https://en.wikipedia.org/wiki/Polyglot_persistence)是指使用不同的数据库来处理不同的需求，在单个软件应用程序中利用每个数据库最擅长的方面来实现最终目标。这是一个很好的解决方案，可以确保您的应用程序正确处理您的数据，而不是试图用一种数据库类型来满足您的所有需求。一个明显的例子是擅长处理结构化数据的 SQL 和最擅长处理非结构化数据的 NoSQL。

让我们来看几个多语言持久性分析:

### 使用的数据库类型的平均数

平均而言，我们发现公司在一个组织内的应用程序中使用 3.1 数据库类型。超过 1/4 的组织利用单一数据库类型，有些组织报告使用了多达 9 种不同的数据库类型:

[![Average Number of Database Types Used in an Organization - ScaleGrid Report](img/7e275eefc3848134d245cd581ba94bd0.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/Average-Number-of-Database-Types-Used-in-an-Organization-ScaleGrid-Report.png)

### 基础结构使用的数据库类型的平均数量

那么，这个数字在不同的基础设施类型中是如何细分的呢？我们发现混合云部署最有可能利用多种数据库类型，平均一次 4.33 种数据库类型。

内部部署通常利用 3.26 种不同的数据库类型，公共云最低，在其组织内平均利用 3.05 种数据库类型。

[![Average Number of Database Used On-Premise vs Public Cloud vs Hybrid Cloud - ScaleGrid Report](img/28ea74668b7747d4d7da072a8b595177.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/Average-Number-of-Database-Used-On-Premise-vs-Public-Cloud-vs-Hybrid-Cloud.png)

### 最常用的数据库类型

现在让我们仔细看看在单个应用程序中最常一起使用的数据库类型。

在下面的图表中，左栏中的数据库代表该数据库类型的样本大小，顶部列出的数据库代表与该数据库类型相结合的百分比。蓝色突出显示的单元格代表 100%的部署组合，而黄色代表 0%的组合。

因此，正如我们在下面的数据库组合热图中看到的，MySQL 是我们最常与其他数据库类型组合的数据库。但是，虽然其他数据库类型经常与 MySQL 结合使用，但这并不意味着 MySQL 部署总是使用其他数据库类型。这可以在 MySQL 的第一行中看到，因为与 MySQL 的第一列相比，这些是更浅的蓝色到黄色，第一列显示了与代表 100%组合的蓝色更高的颜色匹配。

用黑色边框突出显示的单元格表示仅利用这一种数据库类型的部署，其中仅 MySQL 就占其部署的 23%。

[![Percent of Database Deployments Used With Another Database Type - ScaleGrid Report](img/a5bcebfa407badccd258ee96e5d0af84.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/Percent-of-Database-Deployments-Used-With-Another-Database-Type-ScaleGrid-Report.jpg) 我们还可以看到 DB2 的类似趋势，其中 Db2 的底部一行显示它与 MySQL、PostgreSQL、Cassandra、Oracle 和 SQL Server 的利用率很高，但除了 SQL Server 之外，其他数据库部署也利用 Db2 的比例非常低，SQL Server 在 50%的部署中也使用 Db2。

### SQL 与 NoSQL 开源数据库的流行程度

最后但同样重要的是，我们比较了 SQL 和 NoSQL 的开源数据库报告。SQL 占开源数据库的 3/5 以上，占 60.6%，而 NoSQL 占 39.4%。

[![SQL vs NoSQL Open Source Database Popularity - ScaleGrid Report](img/ae61c796a8c9b8431c8b204639f0d299.png)](https://scalegrid.io/blog/wp-content/uploads/2019/06/SQL-vs-NoSQL-Open-Source-Database-Popularity.png)

我们希望这些数据库趋势是有见地的，并引发了一些新的想法或验证您当前的数据库策略！请在下面的评论中告诉我们您的想法，并让我们知道您是否希望在我们的下一份数据库趋势报告中看到具体的分析！查看我们的其他报告，深入了解数据库领域的趋势:

*   [2019 PostgreSQL 趋势报告:私有云与公共云、迁移、数据库组合&使用的主要原因](https://scalegrid.io/blog/2019-postgresql-trends-report-private-vs-public-cloud-migrations-database-combinations-top-reasons-used/ "2019 PostgreSQL Trends Report: Private vs. Public Cloud, Migrations, Database Combinations & Top Reasons Used")
*   [2019 年数据库趋势–SQL 与 NoSQL、顶级数据库、单数据库与多数据库使用](https://scalegrid.io/blog/2019-database-trends-sql-vs-nosql-top-databases-single-vs-multiple-database-use/ "2019 Database Trends – SQL vs. NoSQL, Top Databases, Single vs. Multiple Database Use")
*   [最新 PostgreSQL 趋势:最耗时的任务&要跟踪的重要指标](https://scalegrid.io/blog/latest-postgresql-trends-most-time-consuming-tasks-important-metrics-to-track/ "Latest PostgreSQL Trends: Most Time-Consuming Tasks & Important Metrics to Track")