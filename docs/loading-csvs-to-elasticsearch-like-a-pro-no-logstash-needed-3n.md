# 像专业人士一样将 CSV 加载到 elasticsearch(不需要 Logstash)

> 原文：<https://dev.to/moshe/loading-csvs-to-elasticsearch-like-a-pro-no-logstash-needed-3n>

作为一名 Elasticsearch 用户，我在许多地方(Twitter、Reddit、脸书、StackOverflow)订阅了 elasticsearch 群组和频道，我不断看到人们在开始使用 elasticsearch 时遇到困难，尤其是在加载大量 CSV 文件时。

```
Hey does anyone know how can i upload multiple CSV files to Kibana? 
```

我看到的第一个错误是使用 Logstash 将 CSV 文件加载到 elasticsearch。我将 Logstash 视为 elasticsearch 的流媒体服务，而不是从静态源(如 CSV)向 elasticsearch 加载数据的工具。

## 使用 Elasticsearch Loader 将 CSV 加载到 Elasticsearch

我在 Elasticsearch 的 bulk API 上写了一个包装器，以减少将文件加载到 Elasticsearch 的障碍。用法很简单，从你的终端运行一个命令。

### 安装

`pip install elasticsearch-loader`

### 插件

有几个插件像 Parquet、Redis 和 S3 作为输入源，在 [readme](https://github.com/moshe/elasticsearch_loader) 中阅读更多关于它们的信息

### 用法及举例

将 CSV 加载到在本地主机
`elasticsearch_loader --index incidents csv file1.csv file2.csv`上运行的 elasticsearch 中

从 URL 加载 CSV 并指定 id 字段
`elasticsearch_loader --index data --id-field state csv https://raw.githubusercontent.com/jakevdp/data-USstates/master/state-abbrevs.csv`

从 stdin
`generate_data | elasticsearch_loader --index data csv -`读取 CSV 数据

更多的例子和文档，请点击 [elasticsearch-loader 的自述文件](https://github.com/moshe/elasticsearch_loader)