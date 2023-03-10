# 使用管道在弹性搜索索引中传送文档

> 原文：<https://dev.to/bluttringer/using-pipelines-to-route-documents-in-elasticsearch-indices-48i>

# 路由文件

当应用程序需要向 Elasticsearch 添加文档时，它们必须首先知道什么是目标索引。

当您遇到某种类型的文档总是指向某个特定索引的小情况时，这一点似乎很明显，但是当您的索引的名称根据各种参数(无论它们是系统外部的，例如当前日期，还是您试图存储的文档内部的，大多数情况下是文档中某个字段的值)而变化时，这一点就有点棘手了。当最后一种情况发生时(我们指的是索引名称可以变化的情况)，在向 Elasticsearch 发出索引命令之前，由您的应用程序计算目标索引的名称。

此外——即使一开始看起来像是反模式——您可能有几个应用程序需要在名称可能会改变的索引中索引相同类型的文档。现在，您必须维护跨几个组件重复的索引名称计算的逻辑:从可维护性和敏捷性的角度来看，这不是一个好消息。

[![](img/1640dc86de7e90485b4480b9908bcba8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bJni_rua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ec8lzzupjawcprdomoa5.jpeg)

Logstash —一个众所周知的弹性堆栈成员——可以很好地帮助集中这样一个逻辑，但代价是维护另一个运行的软件，这需要配置、知识等。

我们在本文中想要提出的是一个解决方案，通过将索引名计算委托给 Elasticsearch 而不是我们的应用程序来解决这个问题。

# 用例:基于时间的索引

这是一个众所周知的用例，通常在您想要处理日志时发现。这个想法是在索引中索引文档，索引的名称由一个根名称和一个从日志事件日期开始计算的值组成。这个日期实际上是您要索引的文档的一个字段。

这不是本文的重点，但是以这种方式索引文档有几个优点，包括更容易的数据管理、支持冷/热体系结构等。

我们举个例子。

假设我们必须处理来自多个来源的数据，例如物联网。我们的每个对象每分钟都向不同的后端发送一些数据(是的，这真的很可悲，但是我们的对象不通过相同的网络进行交流，所以选择通过几个系统来处理这个问题)。
对象发送的数据被转换成如下所示的 json 文档:

```
POST data/_doc?pipeline=compute-index-name
{
  "objectId": 1234,
  "manufacturer": "SHIELD",
  "payload": "some_data",
  "date": "2019-04-01T12:10:30.000Z"
} 
```

我们有一个用于传输数据的对象的 UID、一个制造商 ID、一个有效载荷部分和一个日期字段。

# 指标名称计算

假设我们想将对象的数据存储在一个名为`data-{YYYYMMDD}`的索引中，其中根名称是后跟日期模式的数据。

基于后一个例子，后端收到这个文档应该怎么做？

首先，它必须解析它以提取日期字段的值，然后它必须根据在文档中找到的日期计算目标索引名。最后，它向 Elasticsearch 发出索引请求，搜索刚刚计算出名称的索引。

```
document.date = "2019-04-01T12:10:30Z"
index.name = "data-" + "20190401" 
```

在我们的例子中，我们有几个后端必须知道如何计算索引名，因此必须知道索引的命名逻辑。

如果索引名的计算直接由 Elasticsearch 来做，不是更聪明吗？

# 管道电源

[![](img/f8e0144ccd1eb3cc89ebf7f7ddc2d266.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tCxeFhGB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7rsgsgr6n7oxt21810yg.jpeg)

从 Elasticsearch 的第 5 版开始，我们现在有了一种叫做[摄取](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/ingest.html)的节点。

默认情况下，集群的所有节点都具有*摄取*类型。

这些节点有能力在索引文档之前执行所谓的*管道*。管道是一组*处理器*，每个处理器都能以某种特定的方式转换输入文档。
这里有用的是，管道不仅可以转换文档的内部数据，还可以修改文档元数据，特别是它的 _ *index* 属性。

现在让我们回到我们的例子。

我们建议定义一个管道来完成这项工作，而不是将索引名称的计算委托给应用程序。

幸运的是，有一个内置处理器可以做到这一点:[日期索引名称处理器](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/date-index-name-processor.html#date-index-name-processor)！
根据文档，该处理器允许您定义包含日期的字段名称、索引的根名称(前缀)以及计算附加到该前缀的日期的舍入方法。

如果我们想将我们的物联网数据添加到模式为`data-{YYYYMMDD}`的索引中，我们只需创建一个管道，如下所示:

```
PUT _ingest/pipeline/compute-index-name
{
  "description": "Set the document destination index by appending a prefix and its 'date' field",
  "processors": [
    {
      "date_index_name": {
        "field": "date",
        "index_name_prefix": "data-",
        "date_rounding": "d",
        "index_name_format": "yyyyMMdd"
      }
    }
  ]
} 
```

# 一个指标=一条管道？

好了，现在我们知道了如何定义一个管道来为一个特定的目标索引建立一个名称。但是我们可以通过操作文档元数据做更多的事情！

假设我们有不同类型的文档，每个文档都有一个`date`字段，但是需要在不同的索引中建立索引。

对于每种文档类型，计算目标索引名称的逻辑是相同的，但是使用上述策略将导致创建几个管道。让我们试着做一些更简单和可重复使用的东西。

回到我们的例子，我们现在有两种文档类型:一种需要在`data-{YYYYMMDD}`索引中建立索引(和以前一样),另一种的目标是名为`new_data-{YYYYMMDD}`的索引。

目的地为`new_data`的文档具有以下结构:

```
{
  "newObjectId": 1234,
  "source": "HYDRA",
  "payload": "some_data",
  "date": "2019-04-02T13:10:30.000Z"
} 
```

该结构与标准的 *IoT* 文档略有不同，但是这里重要的是`date`字段在两个映射中都存在。

现在，我们想要定义一个管道来计算两种文档类型的目标索引。我们所要做的就是通过分析请求的目的地来构建目的地索引名，就像通过 index API 发出的那样。

```
PUT _ingest/pipeline/compute-index-name
{
  "description": "Set the document destination index by appending the requested index and its 'date' field",
  "processors": [
    {
      "date_index_name": {
        "field": "date",
        "index_name_prefix": "{{ _index }}-",
        "date_rounding": "d",
        "index_name_format": "yyyyMMdd"
      }
    }
  ]
} 
```

请注意，索引名称前缀现在位于名为`_index`的索引元数据字段中。通过使用这个字段，我们的管道现在是*通用的*，并且可以用于任何索引——假设目标索引是根据相同的规则计算的。

# 使用我们的“路由”管道

现在我们有了一个通用管道，它能够根据文档的日期字段计算目标索引的名称，让我们看看如何告诉 Elasticsearch 使用它。

有两种方法可以告诉 Elasticsearch 使用管道，让我们来评估一下。

## 索引 API 调用

第一个也是最简单的解决方案是使用 Index API 的`pipeline`参数。
换句话说:每次你想索引一个文档，你必须告诉 Elasticsearch 要使用的管道。

```
POST data/_doc?pipeline=compute-index-name
{
  "objectId": 1234,
  "manufacturer": "SHIELD",
  "payload": "some_data",
  "date": "2019-04-01T12:10:30.000Z"
} 
```

现在，每次我们通过指示`compute-index-name`管道将文档添加到索引中时，该文档将被添加到正确的基于时间的索引中。在这个例子中，目的地索引将是`data-20190401`。

我们给 Index API 的`data`索引呢？它可以被看作是一个*假的*索引:它只是用来执行 API 调用和作为*真正的*目的地索引的根，它不必存在！

## 默认管道:引入“虚拟索引”

索引默认管道是使用管道的另一种有用方式:当您创建一个索引时，有一个名为`index.default_pipeline`的设置，它可以被设置为一个管道的名称，每当您将一个文档添加到相应的索引并且没有管道被添加到 API 调用时，就会执行该管道。在索引文档时，还可以通过使用名为`_none`的特殊管道绕过这个默认索引。通过使用这个特性，您可以定义一个我称之为“虚拟索引”的东西，并将它与一个默认管道相关联，该管道将充当我们在上面看到的路由管道。

让我们把这个应用到我们的例子中。

我们假设我们的通用路由管道`compute-index-name`已经创建。我们现在可以创建一个名为`data`的索引，它将使用这个管道作为默认管道。

```
PUT data
{
  "settings" : {
    "index" : {
      "number_of_shards" : 3, 
      "number_of_replicas" : 1,
      "default_pipeline" : "compute-index-name"
    }
  }
} 
```

现在每次我们会要求 Elasticsearch 在`data`索引中索引一个文档，`compute-index-name`管道会负责这个文档的真正路由。因此，数据索引中永远不会有一个单独的文档，但是我们完全将调用管道的责任委托给 Elasticsearch。

# 结论

我们刚刚在这里看到了如何利用 Elasticsearch 中的管道功能根据文档的内在属性来路由文档。

管道不仅仅是 Logstash 过滤器的替代品:您可以定义复杂的管道，使用几个处理器(一个特定的处理器甚至允许您调用另一个管道)、条件等等。

在我看来，本文末尾看到的“虚拟索引”可以很有趣。创建这样一个*并不是真正意义上的索引*只是为了创建一个路由管道的入口点，这个特性甚至可以成为 Elasticsearch 的一个新的有用的特性，为什么不呢？

*注意:每个 API 调用都已经使用 Elasticsearch 7.2 的实例进行了测试*