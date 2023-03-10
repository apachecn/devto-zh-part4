# 使用 Pyspark 的 Azure Blob 存储

> 原文：<https://dev.to/luminousmen/azure-blob-storage-with-pyspark-21hd>

[Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/) 是一项用于存储以[任意格式](https://luminousmen.com/post/big-data-file-formats)或二进制数据存储的大量数据的服务。这是一个很好的服务，可以围绕它创建数据仓库或数据湖，以存储预处理或原始数据，供将来分析使用。

在这篇文章中，我将解释如何在 Python 上使用 [Spark framework](https://luminousmen.com/post/spark-anatomy-of-spark-application) 访问 Azure Blob 存储。

* * *

Azure blob 需要安装额外的库来访问其中的数据，因为它使用了[wasb/wabs 协议](https://blogs.msdn.microsoft.com/cindygross/2015/02/04/understanding-wasb-and-hadoop-storage-in-azure/)，而不是事实上的标准 hdfs 协议。Wasbs 协议只是建立在 HDFS API 之上的一个扩展。为了从 Azure blob 访问资源，您需要在提交作业时添加构建的 jar 文件，名为 [hadoop-azure.jar](https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-azure) 和 [azure-storage.jar](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) 到`spark-submit`。

```
$ spark-submit --py-files src.zip \
            --master yarn \
            --deploy-mode=cluster \
            --jars hadoop-azure.jar,azure-storage.jar
            src/app.py 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果您正在使用 Docker 或将应用程序部署到集群中，这里也有一个提示。如果额外的 jar 已经在正确的位置 pyspark 可以找到它——就不需要传递额外的 jar(当然，需要在每个集群节点上都这样做)。小心使用下面的命令—版本/链接可能是错误的！

```
$ wget -nc -nv -O /usr/local/lib/python3.5/site-packages/pyspark/jars/azure-storage-2.2.0.jar http://central.maven.org/maven2/com/microsoft/azure/azure-storage/2.2.0/azure-storage-2.2.0.jar
$ wget -nc -nv -O /usr/local/lib/python3.5/site-packages/pyspark/jars/hadoop-azure-2.7.3.jar http://central.maven.org/maven2/org/apache/hadoop/hadoop-azure/2.7.3/hadoop-azure-2.7.3.jar 
```

Enter fullscreen mode Exit fullscreen mode

在应用程序级别，首先，和 spark 应用程序一样，您需要获取一个 Spark 会话。Spark 会话是集群资源的入口点，用于读取数据和对数据执行 SQL 查询并获得结果。

```
session = SparkSession.builder.getOrCreate() 
```

Enter fullscreen mode Exit fullscreen mode

然后为你的 blob 容器设置一个账户密钥:

```
session.conf.set(
    "fs.azure.account.key.<storage-account-name>.blob.core.windows.net",
    "<your-storage-account-access-key>"
) 
```

Enter fullscreen mode Exit fullscreen mode

或者 SAS 令牌:

```
session.conf.set(
    "fs.azure.sas.<container-name>.blob.core.windows.net",
    "<sas-token>"
) 
```

Enter fullscreen mode Exit fullscreen mode

一旦设置了帐户访问密钥或 SAS 令牌，您就可以读写 Azure blob:

```
sdf = session.read.parquet(
    "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/<prefix>"
)
sdf.show() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**感谢您的阅读！**

有什么问题吗？请在下面留下您的评论，开始精彩的讨论！

查看我的博客或来打个招呼👋在[推特](https://twitter.com/luminousmen)或订阅[我的电报频道](https://t.me/iamluminousmen)。
做好你的计划！