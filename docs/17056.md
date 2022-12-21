# 蒙戈布 4.2，接下来是什么？

> 原文：<https://dev.to/delbussoweb/mongodb-4-2-o-que-vem-por-ai-4b7i>

大家好，今天我带来了在纽约举行的年会上向公众展示的 MongoDB 版本 4.2 的一些重要新闻。连续第五次有机会参加这次活动，花三天时间与朋友见面，与世界各地的人交谈，看蒙戈布各种各样的使用案例和应用。与去年不同，今年的活动更加精彩，带来了我特别喜欢的广告！

## 分布式交易

从 4.0 版开始，mongodb 实施了对多文档的 ACID 交易支持和保修(请记住，这是从一开始就存在于单个文档的！)在复制集中。去年这个广告引起了市场的巨大反响，扩大了应用范围到 mongodb。

如路线图所设想，今年对多文档交易的支持已扩展到分布式模式，在 [sharding](https://docs.mongodb.com/manual/sharding/index.html) 也得到支持。

mongodb 开发团队表示，分布式事务遵循设计目标，并与我们在关系数据库中使用的增长事务行为完全一致。其语法类似，是多指令的，强化了**快照隔离**，使其用法对以前使用过事务的任何人都很熟悉。

另一个好消息是，如果您已经在 mongob 4.0 中使用了事务，则使用 4.2 中的分布式事务没有区别。API 和实现是一致的，即使在文档、集合和数据库、复制集或 shard 群集中使用事务也是如此。完全原子性得以维持，如果一项交易在任何一项伊斯兰教法中都没有“被吃掉”，那么在所有其他参与者中都会停止。

参见分布式事务实现代码实际上没有变化:

```
with client.start_session() as s:
    s.start_transaction()
    collection_one.insert_one(doc_one, session=s) 
    collection_two.insert_one(doc_two, session=s) 
    s.commit_transaction() 
```

Enter fullscreen mode Exit fullscreen mode

只是加强，交易的使用，分散或不应该考虑得很好。80%到 90%的使用 mongodb 的应用程序不需要单个文档的原子保证事务。因此，如果您考虑使用事务，请先查看您的数据模型，看看它是否能解决您的问题。几天之内，我将举几个例子，说明交易的使用是受欢迎的，以及它所带来的影响。

### 可变分片键值

乘坐分布式交易，这是我在广告中最受影响的功能。我没想到会发生这种事！

是的，确实发生了-我...。有关 shard 密钥的更多信息，请参见此处的“T1”，但它决定了如何将数据分区到 shard 群集，并且可以由文档中的一个或多个属性组成。在 4.0 之前，当您选择 shard key 时，此属性的值不能更改，也就是说，如果您选择了客户注册表中的 UF 字段作为 shard key，并且您的客户更改了状态，则必须完全替换文档，从而生成您必须控制的 delete 和 insert

现在，在 4.2 版中，此“限制”已不复存在，使更改此数据变得更加容易。在分布式事务中，mongodb 会自动检测 shard key 值的更新，并将文档移动到正确的 shard！

## 更多运营商，支持复杂的愿望和实时分析

MQL，又称 mongodb 查询语言，表达式非常丰富，我们可以利用各种资源从我们的数据库中提取数据，但缺少某些资源总是造成这样的效果:“伙计，你怎么不这样做？”。在某些情况下，我们不得不求助于该应用程序并在那里这样做，这给一些喜欢与银行一起解决问题的 DBA 带来了极大的挫折感，甚至应该让他们喜欢使用更高级的银行功能。

### 更具表达性的更新

现在，我们可以使用聚合管道指定基于其他字段中的值的更新，并以原子方式应用此更新。

对于 update 或 findAndModify，还可以在值依赖同一文档中的另一个值的字段中键入:

```
db.collection.update(
   {_id:100},
   {
      $set: { "c": { $sum: [ "$a", "$b" ] } }
   }
) 
```

Enter fullscreen mode Exit fullscreen mode

在此示例中，我们将字段 a 和 b 中的值的总和分配给字段 c。这是一个非常简单的案例，但我们可以使用这些聚合运算符来完善我们的更新。

### 新的聚集运算符和表达式

4.2 版增加了对 regex 新增聚合运算符、舍入和三角函数的各种新数学表达式以及诸如$ $ $ now 等“”当前时间的运算符的支持，所有这些都大大减少了应用程序端为解决这些问题而实现的代码量这是一个需要专用 post 的问题，因为聚合管道中有近 100 个表达式，使得计算和转换数据更加容易。

### 实体化视图

新的 aggregation pipeline $merge 输出运算符使您能够创建将按需刷新的实体化视图。也就是说，当这些视图之一使用的集合中的值发生更改时，视图将自动更新。关系数据库中的一项非常常见的功能，使我们能够为分析目的预先计算和存储数据。

使用 4.2 实体化视图，您可以灵活地将结果写入到 shard collections 中，从而可以随着数据量的增长缩放视图。您还可以将这些输出写入不同的数据库，以便更好地将分析工作负载与其他分析工作负载隔离开来。因为具体化视观表储存在共用的 mongbd 集合中，所以您可以使用 mongodb charts、charts 或 Apache Spark Connectors，为每个具体化视观表建立特定的索引，以最佳化资料存取和深入分析。

### 通配符索引

为子文档或子文档数组编制索引始终是一项挑战。因为它是如此重要的资源(“嵌入”文档或价值观)，所以我们不能放弃使用它。将这些值编入索引到 4.0 版之前的解决方案是为每个所需的子文档属性创建索引，或者使用不同的方法使用不同的模式，例如，更丑的模式:

```
{  "k":  "nome do atributo",  "v":  "valor do atributo"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在有了[野生卡片索引](https://docs.mongodb.com/master/release-notes/4.2/#wildcard-indexes)，我们可以更自然地建模，并有高效的支持服务于更多样化的愿望范围。

现在，我们不必预先“猜测”访问模式，而是可以定义一个筛选器，自动索引集合中的所有匹配字段、子文档和数组。出于内存和存储目的，通配符索引实现为“[稀疏索引”](https://docs.mongodb.com/manual/core/index-sparse/index.html)，其中索引仅包含具有值的字段的条目。

万用字元索引不会取代以工作负载为基础的索引计划，但可让您简化多形文件结构(例如产品目录)的设计。

## Tem mais...

以上只是我上周看到的几个要点，我将继续编写并提供使用这些新功能的实际示例，还将介绍 mongodb atlas，它以 Lucene 为引擎，Atlas Datalake，Auto Scaling，retroactive reads，mongob compass and schema validation，zstandard compression，Client-side，提供了令人难以置信的全文搜索服务-我...。

希望你们喜欢！

[![](img/f8f8ae2f99ec8f0d63e309235169e12b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6I6HSmQR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cv4i96fis7qcavdagh8k.JPG)
[![](img/65e2fdbf9ba000850d4bfa3f2e2da3bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uIss55PB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u6xa8sqb1b5mnm2grcxn.JPG)
[![](img/0234b4e537a92c7afa1ff85e92755b43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qzmTSh5a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6u9jxhszpfnz9687drib.JPG)
欧盟、罗德里戈·纳西门托(NetApp)丹尼尔·格拉齐亚尼(MongoDB)

[![](img/d77c49fdca150e82be4241c657e222e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pwYzjgKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9zb1wlsdbg2v55wjq575.JPG) 
Twitter 做麦琳 Diretor 做宣传枢纽 MongoDB