# PySpark 和拼花地板-分析

> 原文：<https://dev.to/danvoyce/pyspark-and-parquet-analysis-4ki4>

众所周知，Pandas 是数据科学家的工具箱，因为 Pandas 库提供了惊人的功能组合。但是熊猫在处理大数据和无法保存在内存中的数据时表现很差。这就是火花出现的地方。Spark 是为大规模数据处理而构建的集群计算系统。

## 为什么用 Spark？

Spark 是一个支持批处理(例如 Hadoop 和 MapReduce)和实时处理(例如 Apache Storm)的框架。

Spark 有许多高级 API 可用。可以用 Scala，Java，Python，r 编码。

## 抽象概念

*   DAG(直接非循环图)-控制执行流程的有向图。

*   Spark context——Spark 集群中的编排。

*   弹性分布式数据集——不可变的，RDD 上的任何活动(如过滤器或地图)都会创建一个新的 RDD。

*   转变-RDD 的活动

*   动作-延迟加载，根据 DAG 按需处理。收集数据、获取计数都是操作。

## py spark 是什么？

Pandas 为您提供了最好的数据分析工具，Spark 为您提供了处理大数据的能力。PySpark 为您提供两全其美的产品。您可以处理大数据，而不必真正了解集群计算的底层机制。PySpark 为您提供了类似熊猫的处理数据框的语法。

比如在熊猫看 CSV:

```
df = pd.read_csv('sample.csv')

#Whereas in PySpark, its very similar syntax as shown below. 
df = spark.read.csv('sample.csv') 
```

尽管这两种语言在语法上有一些不同，但是这两种语言之间的学习曲线很短，您可以更专注于构建应用程序。

## 用拼花地板工作

Parquet 是一个高性能的列格式数据库。
要了解更多关于在列存储中存储数据的好处，请访问:[https://blog . open bridge . com/how-To-be-a-hero-with-powerful-parquet-Google-and-Amazon-F2 AE 0 f 35 ee 04](https://blog.openbridge.com/how-to-be-a-hero-with-powerful-parquet-google-and-amazon-f2ae0f35ee04)。

要从 s3 中读取一个 parquet 文件，我们可以使用下面的命令:

```
df = spark.read.parquet("s3://path/to/parquet/file.parquet")

#Converting compressed CSV files to parquet, with snappy compression, 
#takes approximately 40 minutes for each day’s worth of POI_EVENTS data. 
#This can be done easily with PySpark using the following code. 
for i in range(1, 31):
    print("Processsing Day %02d" % i)
    csv_path = "s3://test-data/2018/05/%02d/*" % i
    p_path = "s3://locally-sanjay/2018/05/%02d/" % i
    df = sqlContext.read.option("header", "false").csv(csv_path)
    df.write.parquet(p_path, mode='overwrite') 
```

## 使用 PySpark 的缺点

使用 PySpark 的主要缺点是还不支持可视化，并且您需要将数据帧转换为 pandas 来进行可视化，不建议这样做，因为将 PySpark 数据帧转换为 Pandas 数据帧会将所有数据加载到内存中。但是，您可以使用“sample”方法将 PySpark 数据帧的一部分转换为 Pandas，然后将其可视化。

## 使用 PySpark 的最佳实践

*   函数库为大多数转换工作提供了内置函数。然而，您可以编写自己的 Python UDF 来进行转换，但不推荐这样做。内置功能在 Spark 使用的 JVM 内部工作，运行 Python UDF 将使 JVM 启动一个 Python 实例，并将所有数据发送到该实例，并将所有输出从该实例发送回 JVM，这将显著影响性能:

*   不要在数据帧中逐行迭代。

*   不要将整个 PySpark 数据帧转换为 Pandas，始终采样并转换为 Pandas。

## 技术堆栈

在本地测试产品时使用了以下技术组合:

*   具有 1 个主节点和 2 个从节点的 Amazon Spark 集群(标准 EC2 实例)
*   s3 存储拼花文件的存储桶。
*   Zeppelin 笔记本来运行脚本。
*   谷歌云平台上的性能和成本需要测试。

最终，我们选择 ORC 作为我们在本地的主要存储格式，但根据您的具体使用情况，拼花地板也是一个不错的选择。ORC 是本地位置情报平台背后的主要驱动力

| ![](img/a065a57d52758067ecd604eac5642d94.png) | **桑杰·库马尔-数据工程师**数据科学研究生，Spark，Presto，BigQuery 和大数据方面的技能。 |