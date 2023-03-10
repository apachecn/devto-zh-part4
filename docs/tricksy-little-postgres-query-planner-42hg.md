# 狡猾的小 Postgres 查询计划程序

> 原文：<https://dev.to/jgaskins/tricksy-little-postgres-query-planner-42hg>

我们在工作中遇到过这个查询，它慢得令人恼火，在一个相当常见的 HTTP 请求过程中大约 5-10 秒。到目前为止，还没有人能加快它的速度。我们对这个查询运行了`EXPLAIN ANALYZE`,看起来没问题。

如果你从未看过一个`EXPLAIN ANALYZE`结果，它们看起来像这样:

```
 QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=2.93..360.99 rows=50 width=1205) (actual time=42.779..42.779 rows=0 loops=1)
   ->  Hash Left Join  (cost=2.93..1879.13 rows=262 width=1205) (actual time=42.778..42.778 rows=0 loops=1)
         Hash Cond: (table1.id = table2.table1_id)
         Filter: (((table2.table3_id = 712581) AND table1.flag) OR ((table1.table4_id = 712581) AND table1.other_flag))
         Rows Removed by Filter: 24211
         ->  Seq Scan on table1  (cost=0.00..1784.11 rows=24211 width=1205) (actual time=0.023..17.043 rows=24211 loops=1)
         ->  Hash  (cost=1.86..1.86 rows=86 width=8) (actual time=0.066..0.066 rows=86 loops=1)
               Buckets: 1024  Batches: 1  Memory Usage: 12kB
               ->  Seq Scan on table2  (cost=0.00..1.86 rows=86 width=8) (actual time=0.012..0.029 rows=86 loops=1)
 Planning Time: 72.257 ms
 Execution Time: 43.034 ms 
```

Enter fullscreen mode Exit fullscreen mode

其中重要的部分是像`Hash Left Join`、`Seq Scan`和`Hash`这样的东西。另一个常见的是`Index Scan`。它大致解释为这样的时间复杂度:

| 计划 | 复杂性 |  |
| --- | --- | --- |
| `Hash` | [O(1)](https://en.wikipedia.org/wiki/Time_complexity#Constant_time) | 由内存中的哈希表支持 |
| `Index Scan` | [O(对数 n)](https://en.wikipedia.org/wiki/Time_complexity#Logarithmic_time) | 由磁盘上的二叉树支持 |
| `Seq Scan` | [O(n)](https://en.wikipedia.org/wiki/Time_complexity#Linear_time) | 就像遍历任何列表的元素一样遍历表中的行 |
| `Nested Loop` | [O(n * m)](https://en.wikipedia.org/wiki/Time_complexity#Polynomial_time) | 对一个集合中的行进行迭代，得到另一个集合中的每一行 |

上面的查询计划是我们数据库中的一个实际查询计划(表和列被清理)。它使用了一个`Hash Left Join`和一个`Hash`，所以应该很快！它也使用了一个`Seq Scan`，但是它发生在一个已经被过滤的集合上——这实际上很常见。

那么，如果这个查询的计划如此之快，为什么会如此之慢呢？今天，我决定对我们的生产数据库运行`EXPLAIN`，并意识到为什么:

```
 QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Limit  (cost=0.29..907487.63 rows=50 width=1936) (actual time=54.223..8893.723 rows=1 loops=1)
   ->  Nested Loop Left Join  (cost=0.29..1161584.09 rows=64 width=1936) (actual time=54.221..8893.721 rows=1 loops=1)
         Filter: (((table2.table3_id = 712581) AND table1.flag) OR ((table1.table4_id = 712581) AND table1.other_flag))
         Rows Removed by Filter: 2095442
         ->  Seq Scan on table1  (cost=0.00..477337.61 rows=2098223 width=1936) (actual time=0.021..4430.654 rows=2095443 loops=1)
               Filter: (flag OR ((table4_id = 712581) AND other_flag))
               Rows Removed by Filter: 392143
         ->  Index Scan using index_1 on table3  (cost=0.29..0.31 rows=1 width=8) (actual time=0.001..0.001 rows=0 loops=2095443)
               Index Cond: (table1.id = table1_id)
 Planning time: 4.166 ms
 Execution time: 8893.867 ms 
```

Enter fullscreen mode Exit fullscreen mode

它使用非常不同的数据结构。看不见一个人。我们的`LEFT JOIN`不再由`Hash`做后盾，而是作为`Nested Loop`运行。那就是*粗*。

很可能不再使用`Hash`方法进行连接，因为对于包含数百万行的表来说，内存中的散列对于单个查询来说可能是不可行的。不是因为它不能对单个查询这样做，而是因为它不能同时对所有查询这样做。

考虑到这一点，它使用较慢的查询计划也就说得通了。只是当时我们没有想到它会用一个不同的。

这就是为什么每当您想要了解查询的性能时，都需要在生产数据库上进行。您的开发或暂存数据集不会给您所需的洞察力。

这并不总是可行的，因为一些公司可能对谁可以访问生产数据库有非常严格的政策，但重要的是要有工具来允许对现实世界数据的查询进行分析。

### 我们如何修复它

你可能对我们的解决方案很好奇。我们最终(在几次迭代之后)将`LEFT JOIN`完全移除，因为我们无法让它作为除了`Nested Loop`之外的任何东西运行，这大约是查询运行时的 99%。相反，由于我们使用`OR`的方式，我们选择运行类似于
的`UNION`查询

```
SELECT *
FROM table1
WHERE table1.table3_id = 123456
AND flag

UNION

SELECT table1.*
FROM table1
INNER JOIN table2 ON table1.id = table2.table1_id
WHERE table2.table3_id = 123456
AND other_flag 
```

Enter fullscreen mode Exit fullscreen mode

实际的查询稍微复杂一些，因为它是由一个 ORM 生成的，但它与此相当。查询计划几乎完全相同。

查询的第一部分是我们开始做`LEFT JOIN`的原因。因为另一个表中的相关行只有在设置了`other_flag`的情况下才能得到保证，所以我们不能在整个过程中使用`INNER JOIN`。

这使得我们的查询时间从 7-10 秒(取决于数据库负载)下降到非常一致的 125 秒，查询延迟减少了 99.9982-99.99875%。事实上，我不得不检查以确保我们甚至击中了正确的数据库，因为这个数字看起来与我预期的完全不一样。但毫无疑问，这正是我们需要它做的，它让 Postgres 使用 4 个索引(以前它只使用 1 个),并可能使*真正*很好地利用查询缓存。

对于一天的工作来说还不错！