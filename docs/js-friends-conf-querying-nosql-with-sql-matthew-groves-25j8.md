# JS&Friends Conf:用 SQL 查询 NoSQL

> 原文：<https://dev.to/aromig/js-friends-conf-querying-nosql-with-sql-matthew-groves-25j8>

## 用 SQL 查询 NoSQL-[马修·格罗夫斯](https://twitter.com/mgroves)

[![Matthew Groves](img/14d7411f2818dc3860410e979afd7f9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bGTPNog3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xm0xgbfksx3mi5a6mibw.jpg)

我一直想多读一些关于 NoSQL 数据库的知识，以及它们与关系数据库的不同之处。我使用 SQL 已经 14 年了，所以关系设计和结构在我对表模式的看法中非常重要。

Matthew 解释了使用 NoSQL 优于 SQL 的优点:可伸缩性(如果需要，可以部署更多的服务器或集群它们)、灵活性(不需要严格的模式)和性能。他指出，我们不必完全放弃关系数据库——它们可以共存。

关系数据库通过外键链接不同的表，而 JSON 文档模型(NoSQL 使用的格式)可以将相关数据作为主记录放在同一个文档中。如果数组包含子数据的多个实例，那么这些额外的部分可能是数组对象。可以通过一个键来创建和引用用于单独概念的其他文档。

一旦他解释了 NoSQL 背后的概念，他向我们展示了可以创建查询(非常类似于 SQL)来查找数据。这被称为 N1QL(发音为 *nickel* )，是一种以 Couchbase 为中心的查询语言。Azure Cosmos 也有类似的东西。其他 NoSQL 平台需要使用集合函数，对我来说，乍一看不太可读。

```
SELECT field
FROM `bucket` /* buckets contain logical groups of data-items */
WHERE otherfield = somevalue
/* JOINs are possible too! */
JOIN `otherbucket` ON (bucket.id = META(otherbucket).id)
LIMIT number; 
```

Enter fullscreen mode Exit fullscreen mode

N1QL 查询返回我们可以用本地编码语言解析的 JSON 对象。

如果从这次演讲中有什么收获的话，马修说是这三件事:

*   选择正确的应用。无论是 NoSQL 还是 SQL，使用有意义的东西。
*   JSON 数据的建模方式不同。文档的结构可能与表格非常不同。
*   用 SQL 查询 NoSQL 数据。写起来容易多了。

[←回主 JS &好友文章](https://dev.to/aromig/my-first-dev-conference-javascript-friends-fh7)