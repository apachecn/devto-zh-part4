# 从 Azure Databricks 连接到 Azure 数据仓库

> 原文：<https://dev.to/fruiza/connect-to-azure-data-warehouse-from-azure-databricks-jl2>

## 创建秘密范围

秘密范围将允许您使用 Azure KeyVault 下载所有秘密信息以连接到 Azure 数据仓库，例如:用户名/密码等。在我们的例子中，这将是整个连接字符串。

导航到 https://{ region } . azuredatabricks . net/？o = { object _ id } #机密/创建范围

[![](img/7b9bb490d16ca70e26e61cdd509cee9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dvvp-fY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uiq4eiihcf9ekbv3hha9.png)

从您的 Azure KeyVault 属性获取 DNS 和资源 ID

[![](img/6a3699d809b526c84b93c7d8fc1ed14c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CerZXbK1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/erftnman1x64roqfxjhm.png)

### 从 azure keyvault 获取需要的秘密，设置 Spark 配置

```
blob_storage_url = dbutils.secrets.get(scope = "databricks_scope", key = "blobstorageurl")

blob_storage_key = dbutils.secrets.get(scope = "databricks_scope", key = "blobstoragekey")

adw_connection = dbutils.secrets.get(scope = "databricks_scope", key = "dwconnection")

blob_storage_temp_dir = dbutils.secrets.get(scope = "databricks_scope", key = "blobstoragetempdir")

spark.conf.set(blob_storage_url, blob_storage_key) 
```

## 火花驱动到 SQL DW

Spark 驱动程序使用用户名和密码通过 JDBC 连接到 SQL DW。我们建议您使用 Azure portal 提供的连接字符串，该字符串通过 JDBC 连接为 Spark 驱动程序和 SQL DW 实例之间发送的所有数据启用安全套接字层(SSL)加密。要验证 SSL 加密是否已启用，可以在连接字符串中搜索 encrypt=true。为了允许 Spark 驱动程序访问 SQL DW，我们建议您通过 Azure portal 在 SQL DW 服务器的防火墙窗格上将允许访问 Azure 服务设置为 ON。该设置允许来自所有 Azure IP 地址和所有 Azure 子网的通信，这允许 Spark 驱动程序到达 SQL DW 实例。

更多信息请见[此处](https://docs.databricks.com/spark/latest/data-sources/azure/sql-data-warehouse.html)

### 将 SQL DW 查询中的数据加载到 Spark 数据帧中。

```
df = (spark
      .read
      .format("com.databricks.spark.sqldw")
      .option("url", adw_connection)
      .option("tempDir", blob_storage_temp_dir)
      .option("forwardSparkAzureStorageCredentials", "true")
      .option("query", "select count(*) as counter from my_table")).load()

df.show() 
```

就是这样！又快又简单。