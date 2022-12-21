# 使用摄取管道通过查询进行弹性搜索更新

> 原文：<https://dev.to/shubho/elastic-search-update-by-query-with-ingest-pipeline-2k16>

我应该从忏悔开始。我喜欢弹性搜索。给它输入任何文档，然后从中搜索甚至精确的术语，感觉棒极了。然而，当我开始在工作中创建和管理集群时，这简直是一场噩梦。我是个初学者，所以很多概念需要时间来理解。我在网上找到的大部分教程都是 1.x-2.x 版本，但是 ES 已经是 5.x 版本了，而且刚刚开始 6.x 系列。初始群集是在 2.x 版本上创建的，要将其升级到最新的 6.x 系列，需要先升级到 5.x 系列，然后再升级到 6.x 系列。

由于这仅仅是一个月的数据输入，数据并不庞大，升级需要 2-3 天(由于涉及到重新索引)。我们的集群是索引和查询密集型的。然而，索引是基于最大努力的。整个流水线允许系统在不同的时间索引四至五次。因此，当我重新索引和升级我的集群时，新的数据仍然会进入旧的集群。重新索引完成后，我只使用新数据进行了另一次重新索引。此外，在重新索引期间，任何进一步的数据都会被丢弃。因为每个索引管道在不同的时间间隔有大约 5 次尝试，最终被丢弃的数据回到新系统中。

当集群中的整体数据负载较少时，上述过程很容易。目前，我们的集群一年拥有大约 15TB 的数据，支持 3 个不同的系统。如今，重建索引是不可能的。大多数指数都很热门，而且一直在被使用。

最近，我有一个用不同值更新一对索引字段的需求。好的一面是它不需要改变映射。字段类型没有改变。只是数据需要更新。

例如，假设字段名为“jobIdentifier”。大多数文档都将该值作为字母数字。然而，一些文件有类似“213452-2-12932- <alphanumberic>”的条目。要求是将这些值标准化为仅<alphanumberic>。很简单，对吧？</alphanumberic></alphanumberic>

## 使用正则表达式更新 Elasticsearch 字段值

最直接的方法是使用无痛脚本语言的正则表达式。来自弹性搜索文档，

> 无痛是一种简单、安全的脚本语言，专门设计用于 Elasticsearch。

使用无痛脚本语言来满足上述需求，我们可以像这样编写正则表达式。

```
{  "script":  {  "lang":  "painless",  "source":  "ctx._source.jobIdentifier = /[0-9]+-[0-9]+-[0-9]+-(.*)/.matcher(ctx._source.jobIdentifier).replaceAll('$1')"  }  } 
```

所以在正则表达式中，它将字段“jobIdentifier”与类型为`[0-9]+-[0-9]+-[0-9]+-(.*)`的值匹配，并用分组模式`.*`替换它。从而删除前面的 3 个数字以及连字符分隔符。所以像`213452-2-12932-Some_random_value`这样的值就变成了`Some_random_value`。

然而，由于 Java 中的 StackOverflow 问题，无痛脚本中的正则表达式在最新版本的 Elasticsearch 中被默认禁用。[[https://github.com/elastic/elasticsearch/pull/20427](https://github.com/elastic/elasticsearch/pull/20427)该问题及其相关链接详细描述了该修复。这让我相信我可以找到解决上述问题的其他方法。除非必要，否则我不想在脚本中启用正则表达式。

## 通过查询摄取管道并更新

Elasticsearch 中的摄取节点用于在文档被索引之前对其进行预处理。默认情况下，群集中的所有节点都是接收节点。如果摄取过程是资源密集型的，则可以将它们分开。管道定义了预处理器。它们包含一个“描述”和一个“处理器”。处理程序是对文档执行的一系列步骤。它们可以包含 Logstash grok 模式和无痛脚本。

首先，让我们看看管道的定义。

```
curl -X PUT "localhost:9200/_ingest/pipeline/my-pipeline-id" -H 'Content-Type: application/json' -d'
{
    description": "Used to update in place - Remove the alphanumeric ids from jobIdentifier in my_index index",
    "processors": [
        {
            "grok": {
                "field": "jobIdentifier",
                "patterns": [
                    "%{NEWFIELDIDS:eID}-%{NEWFIELDIDS:eSEQID}-%{NEWFIELDIDS:e2ID}-%{GREEDYDATA:REST}"
                ],
                "pattern_definitions": {
                    "NEWFIELDIDS": "[0-9]+"
                },
                "ignore_missing": true
            }
        },
        {
            "script": {
                "lang": "painless",
                "inline": "jobIdentifier = ctx.REST"
            }
        },
        {
            "script": {
                "lang": "painless",
                "inline": "ctx.remove('eID'); ctx.remove('eSEQID'); ctx.remove('e2ID');  ctx.remove('REST')"
            }
        }
    ]
} 
```

这些文件要经过流水线上的三个处理器。

```
"grok":  {  "field":  "jobIdentifier",  "patterns":  [  "%{NEWFIELDIDS:eID}-%{NEWFIELDIDS:eSEQID}-%{NEWFIELDIDS:e2ID}-%{GREEDYDATA:REST}"  ],  "pattern_definitions":  {  "NEWFIELDIDS":  "[0-9]+"  },  "ignore_missing":  true  } 
```

匹配字段“作业标识符”。创建一个名为“NEWFIELDIDS”的模式，其中包含一个或多个数字。如果字段具有“数字-数字-数字-其他数据”的序列，则每个数字集被分配一个字段名(eID，eSEQID，e2ID)。匹配模式的其余部分，直到结束，并将其分配给字段“rest”。这个匹配是使用 Logstash grok 模式中的内置类型“GREEDYDATA”完成的。在这一步结束时，将四个新字段(eID、eSEQID、e2ID、REST)添加到文档中。但是，如前所述，可能存在上述字段模式不匹配的文档。在这种情况下，管道停止。我们不希望这样。因此，我们将“ignore_missing”添加为“true”。

```
"script":  {  "lang":  "painless",  "inline":  "jobIdentifier = ctx.REST"  } 
```

现在我们运行一个无痛脚本。我们采用新创建的字段“REST ”,并将“jobIdentifier”字段重新分配为这个值。

```
"script":  {  "lang":  "painless",  "inline":  "ctx.remove('eID'); ctx.remove('eSEQID'); ctx.remove('e2ID');  ctx.remove('REST')"  } 
```

最后，我们删除步骤 1 中新创建的字段(eID、eSEQID、e2ID、REST ),因为我们不再需要它们。

解释看起来很庞大，但是我们基本上匹配了模式，更新了字段，然后删除了额外创建的新字段。

最后，使用新创建的管道(由“my-pipeline-id”标识)，我们对索引运行 Update by 查询。我们匹配其字段匹配必要模式的文档，并通过管道传递它们。我们可以在现场使用多个索引来运行来自多个索引的文档。我们使用`wait_for_completion`作为 false，因此查询立即返回，更新在后台进行。

```
curl -X POST "localhost:9200/my_index/_update_by_query?pipeline=my-pipeline-id&wait_for_completion=false"
{
    "query": {
        "bool": {
            "must": [
                {
                    "regexp": {
                        "jobIdentifier": "[0-9]+-[0-9]+-[0-9]+-(.*)"
                    }
                }
            ]
        }
    }
} 
```

## 结论

在 Elasticsearch 中匹配/更新字段时，我们可能不需要脚本中昂贵的正则表达式。与其在我们的集群中盲目地启用它，我们可以首先尝试一个管道和“通过查询更新”，这在大多数常见情况下肯定可以满足我们的需求。