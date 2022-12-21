# 何时将非聚集索引放在聚集键上

> 原文：<https://dev.to/am2/when-to-put-a-non-clustered-index-on-your-clustering-key-4f1l>

<figure>[![](img/e20732b1895780a819f258564bd546eb.png)](https://www.youtube.com/watch?v=uvWL6_A7uQc) 

<figcaption id="caption-attachment-2121">这些葡萄藤被赤霞珠簇拥着。</figcaption>

</figure>

#### 首先，让我们记住聚集索引和非聚集索引的区别&

**聚集索引**由键列组织。它还包括每隔一列作为行结构的一部分(即，它包含整行)。

**非聚集索引**也是由键列组织的。它隐式地包括聚集键列(如果表是聚集的)，或者指向行的指针(如果表是堆)。如果显式指定了任何`INCLUDE`列，它们也将包含在索引结构中(但是这些包含的列不影响顺序)。

## 我应该把两者放在同一列吗？

有一种极端的情况，让非聚集索引“复制”聚集索引是有意义的。如果您有一个经常*扫描*表的查询，并且只使用聚集键列，那么查询优化器将更喜欢使用非聚集索引。非聚集索引不包含完整的行数据，因此它将占用较少的物理空间。因为它占用较少的物理空间，所以 SQL Server 可以用较少的 io 扫描该表，并出于性能原因利用它。

我还会注意到，SQL Server 在*搜索*时会做出相同的决定:如果两个索引都是覆盖的[，它会选择使用占用空间最少的那个。](https://sqlsunday.com/2013/02/24/covering-indexes/)

你可以查询`sys.dm_db_index_usage_stats`，可以看到两个指标上`user_seeks`和`user_scans`的数量。这将帮助您了解这两个索引是如何使用的，并确定这两个索引的有用性。

## 好吧，我没有那个角壳，我该掉哪个？

一般来说，大多数表都应该有聚集索引。(那完全是另外一个话题了)。在不知道索引使用或数据访问的任何细节的情况下，我猜如果要删除一个索引，就会删除非聚集索引。

这取决于聚类键、数据、数据访问模式等。，最正确的答案可能是删除聚集索引并创建一个不同的聚集索引。您可能想阅读更多关于[有效聚集索引](https://www.simple-talk.com/sql/learn-sql-server/effective-clustered-indexes/)的内容。

帖子[何时在你的聚簇键](https://am2.co/2019/09/when-to-put-a-non-clustered-index-on-your-clustering-key/)上放一个非聚簇索引最早出现在 [Andy M Mallon - AM](https://am2.co) 上。