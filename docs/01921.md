# 星火是打了类固醇的熊猫

> 原文：<https://dev.to/lukaszkuczynski/spark-is-pandas-on-steroids-3j4g>

看了《如果你爱上了熊猫》，开始思考并行处理是否可以很容易

## 你是谁

让我猜猜。您是一名数据科学家，使用 pandas 和其他酷库在您的机器上运行预处理和建模。或者你只是一个成熟的软件工程师，曾经听说过并行处理，但从未见过真正的用例。即使你不属于任何一个群体，想想看:

> 为什么我需要并行处理？

即使你以前没有意识到，为什么它可能是你关心的？我跟大家分享一下我的经历吧。最近，当`fbprophet`被用来预测几千个系列时，我们有了一个管道。完成整个过程花了很长时间。一个想法:也许在一些 Azure 快速计算设备上运行它会加快速度？是的，但是仍然要等几个小时。那么这里的瓶颈是什么呢？`fbprophet`所有这些处理都是单机解决方案。您不能只是将它分散到要并行处理的节点上。但那会很好，对吧？您说:这 10k 行放在计算#1，其他 10k 行放在#2，依此类推。想要实现它吗？

## 火花就是答案

Apache Spark 的创建是为了让并行处理成为可能。但是之前是什么呢？Hadoop 的时代已经到来。处理大数据的工具大生态系统。也许现在一些专家声称:它已经过时了，但它是未来的一大飞跃——你可以**把你的大工作分成小块，然后把它们送到几台机器上处理**。然后结果就收集好了，给你！

您可以利用现有的商用机器，而无需购买昂贵的顶级机器。您可以横向扩展(而不是纵向扩展)！但是使用 Hadoop 有一些限制。你必须坚持使用 MapReduce 和 Java 语言，这是唯一的构建和 API 语言。此外，所有计算步骤都大量使用磁盘，这是该架构的最大瓶颈之一。

所以这就是 Spark 推出的原因。与 Hadoop 相比，该引擎的主要优势是在内存中处理所有数据。你不会从驱动这个过程的机器的角度来思考。您可以根据需要实例化任意数量的*工人*。所有这些——组成了一个*集群*——是你手中相当有力的工具。更重要的是，您并不拘泥于基于 Java 的 MapReduce，但是手边有很好的 DataFrame API，支持多种语言，如 Python、Scala、Java、R，甚至 SQL。听起来很贵而且几乎买不到？恰恰相反！

## 怎么才能得到

### Spark 还是托管 Spark？

你可以在你的机器上安装 Spark。但是为什么你可以在网上试试呢？免费的！不再需要在你的机器上安装二进制文件来尝试一些东西。甚至不需要在后台的某个地方运行 docker 守护进程。为什么？在你周围有一个幸福(或诅咒)的 PaaS 和 SaaS 丰富。那你呢。你想自己试试 Spark 吗？

### 数据块

让我介绍一下 Databricks。这是一个可在线获得的产品，为广泛理解的分析而创建。它也是由 Azure(微软云产品)托管的，在那里你可以试着运行它。我在这里提到 Azure 是因为这是我第一次遇到 Databricks 的地方。

特别是为了这篇文章，我在 Databricks 的社区版本中创建了一个新帐户，以检查它有多容易。是的，我花了大约 2 分钟创建并启动了我的第一个 Databricks 帐户，没有任何信用卡信息等等，只需填写本页上的表格[。当您准备好集群后，就可以使用 Spark API 来玩它了。](https://databricks.com/signup/signup-community)

## 用例

我不喜欢为了学习而学习。我喜欢注意一些工具如何让我更快地解决问题。也许作为一名 IT 人员，您正在遍历冗长、枯燥的日志文件，以了解应用程序发生了什么。你有千兆字节的日志，你希望看到其中的模式。你可以继续 grepping，但没有…你打算怎么用 Spark 做到这一点？

你不必上传你自己的日志文件来开始在社区版的 Databricks 中使用它。Databricks 上已经有许多数据集可供使用，包括示例日志文件。首先，我们将它们全部读给叫做 RDD 的结构:

```
rdd_original = sc.textFile('/databricks-datasets/sample_logs/')
rdd_original.take(10) 
```

前面的命令只是从提供给一个名为 RDD 的抽象的位置读取所有文件。这一个可用于进一步的变换(映射/减少/过滤)或变换成数据框。完成了一些操作来解析文件行中的数据。为了简单起见，我在本文中跳过了它——不过完整的笔记本[也可以在](https://lukaszkuczynski.github.io/assets/logs-databricks.dbc)中找到。要创建数据框，我们只需提供模式。

```
from pyspark.sql.types import StructField, StructType, IntegerType, StringType, TimestampType

schema = StructType([
  # your data types definitions go here ])

df = rdd_mapped.toDF(schema) 
```

有了数据框架，我们可以开始做一些汇总并得出结论。例如，您可能对特定用户出现不同错误的频率感兴趣。我们可以使用 Python API 或 SQL 来了解它。

```
df_identified = df[df['user'] != '-']
df_grouped = df_identified.groupby(['user','status']).count() 
```

对于你，我亲爱的熊猫开发者，这些操作应该看起来很熟悉，对吗？当然，您可以用 SQL 语法来表达您的查询。

```
df.createOrReplaceTempView('log') 
```

```
select user, status, count(*)
from log
where user <> "-"
group by user, status 
```

Databricks 不仅为您提供了关于云的火花体验，还包含基本的可视化功能。我鼓励您将它们用于科学目的，而不是作为向客户展示数据的工具。

## 总结

这里所做的数据分析在一段时间内可以在[这个公共链接](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/3276244303656844/2182517269869031/287949548447532/latest.html)下公开获得。我也上传了那个作为[的可下载资产在这里](https://lukaszkuczynski.github.io/assets/logs-databricks.dbc)。有了 Spark 特性和 DataFrame API，我们可以做很多事情。重要的是，在处理单台机器难以处理的大量数据时，您可以看到的全部优势。