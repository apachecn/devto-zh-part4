# 使用 MySQL 进行查询优化

> 原文：<https://dev.to/mcloide/query-optimization-with-mysql-322i>

DBA 是一种职业是有原因的，数据库的细微差别可以导致任何人精神错乱，MySQL 的细微差别也不例外。

我并不知道这些细微差别，正如许多阅读本文的人一样，我是一名工程师，每天都要处理 MySQL、复杂的查询和大量数据。谈论性能需要设置一个阈值来理解什么是性能差的查询，为此，我将使用 MySQL 默认参数:1 秒。

即使对于较大的数据集和大量的行，查询也可以在不到 1 秒的时间内完成。MySQL 查询优化引擎真的很好，只要你知道如何正确地编写查询，它会优化它尽可能快地运行。

当谈到让查询执行得更快时，至少有一些关于引擎优化如何工作的基本知识是很重要的，这样就可以利用所有的潜力并避免同样的警告。不幸的是，除非您是 DBA，否则您不太可能真正了解这些细微差别。像理解 MySQL 运行查询的每个部分或每个查询就像运行一个 JOIN 语句一样。
查询构造

要了解如何适当地提高查询的性能，您必须首先了解如何适当地将查询分成几个部分。每个 SELECT 语句都有:

```
SELECT
    columns
    sub-selects
    functions
    improper aggregations-- main table
    joins
    sub-selects-- where clauses-- aggregation
    groupping, ordering, having and limiting

```

### 列

这些部分中的每一部分都会以某种方式影响查询的性能，但是列是唯一可能影响较小的部分。

列部分的警告是如果你有一个子选择或者一个函数调用。如果该函数的性能很差，那么它将影响您的查询性能。子选择也是如此。

最后一点可能影响这部分查询的是不正确的聚合。MySQL 中有相当数量的资源执行速度极快，如 TOP、MAX、MIN 等。

### 主表、连接和子选择

您放置连接的顺序或这里正在读取的表的顺序不会对性能产生太大影响，但是每个表上的行数或子选择的结果会产生影响。只要整个查询可以在内存中执行，或者查询所需的索引部分在内存中可用，索引肯定会对这部分查询有所帮助。当内存中没有这些信息时，数据库将创建一个临时资源来存储结果，然后过滤掉。

处理这个问题的最好方法是只带查询真正需要的表。例如:

```
SELECT f.id, f.name, f.description, f.type_id
  FROM foo f
  JOIN foo_type ft ON ft.id = f.type_id
 WHERE ft.id = 2;

```

如果只从 foo 表中取出 type_id 等于 2 的行，为什么要连接 foo_type，特别是在类型名不可用的情况下。此外，如果类型名是必需的，那么它会是运行时显示的预加载信息吗？

```
SELECT f.id, f.name, f.description, f.type_id
  FROM foo f
 WHERE f.type_id = 2;

```

这里最简单的技巧就是问这些问题:

*   我真的需要这张桌子来达到目的吗？
*   我带这张桌子是因为我可能需要它吗？

### Where 从句

说到性能，Where 子句很有意思。过滤太少会导致更多的行，从而导致速度变慢，但是过滤太多也会导致速度变慢。最好的办法是找一个健康的媒介。

例如:

```
select 
    distinct o.id as order_id
    , round(sum(oi.amount * oi.quantity), 2) as total
  from orders o
  join order_items oi on oi.order_id = o.id
  join order_type ot on ot.id = o.order_type_id
 where o.create_date between '2019-01-01 00:00:00' and '2019-01-01 23:59:59'
   and oi.type_id in (1,2,3)
   and o.type_id = 2
 group by oi.id

```

where 子句必须过滤日期范围、项目类型和订单类型。如果所有这些表(虚构的)都有大量的行(考虑超过 200 万行)，那么即使正确地创建了索引，该操作的开销也会很大。除了 range 子句本身很昂贵之外，您可以尝试其他两个条件，看看哪一个执行得更快。

例如，如果您删除检查订单类型的条件，并将其移动到 SELECT 块，它可以执行得更快。

```
select 
    distinct o.id as order_id
    , round(sum(if o.type_id != 2, 0, oi.amount * oi.quantity), 2) as total
  from orders o
  join order_items oi on oi.order_id = o.id
  join order_type ot on ot.id = o.order_type_id
 where o.create_date between '2019-01-01 00:00:00' and '2019-01-01 23:59:59'
   and oi.type_id in (1,2,3)
 group by oi.id

```

上面查询的最终结果与第一个查询的最终结果相同，但是它执行得更好，因为它在 where 子句上执行的条件更少。

出现这种情况的原因很简单。MySQL 将遍历订单中所有需要的行，然后将这些行与订单项进行匹配，最后将这些行与订单类型进行匹配。在这个结果集被创建之后，不管是不是临时表，它都需要应用每个条件。更少的条件意味着更快，尤其是当您可以将这些条件作为列生成的一部分添加时。

最终的查询应该如下所示:

```
select 
    distinct o.id as order_id
    , round(sum(if o.type_id != 2, 0, oi.amount * oi.quantity), 2) as total
  from orders o
  join order_items oi on oi.order_id = o.id
 where o.create_date between '2019-01-01 00:00:00' and '2019-01-01 23:59:59'
   and oi.type_id in (1,2,3)
 group by oi.id

```

没有必要在那里连接 order 类型，因为它根本不做任何事情，如果连接一个表的理由是确保外键有效，那么，有一个全新的问题需要解决，但不是查询性能。

### 排序、分组、拥有和限制

查询的最后一部分会在几个方面影响查询性能。如果通过文件排序进行应用，排序可能会很慢，缺少分组和限制可能会产生比所需更多的结果，最后，having 子句可能会非常昂贵，尤其是在进行数学比较时，例如

```
having sum(oi.amount * oi.quantity) > 999.00

```

根据查询的不同，having 子句需要计算所有条目，然后过滤出结果。
通过解释保存

我知道对于查询优化来说，所有事情的默认答案都是添加索引。这是个谎言。索引是可以应用的优化的一部分，如果你真的想优化一些东西，那么你必须了解解释的结果，更好的是，解释扩展的结果。

执行 explain 真的很简单，你只需要把它加在查询前面，让它告诉你哪里错了。

```
explain extended select 
    distinct o.id as order_id
    , round(sum(if o.type_id != 2, 0, oi.amount * oi.quantity), 2) as total
  from orders o
  join order_items oi on oi.order_id = o.id
 where o.create_date between '2019-01-01 00:00:00' and '2019-01-01 23:59:59'
   and oi.type_id in (1,2,3)
 group by oi.id

```

从显示的结果来看，您需要注意 6 列内容:

*   类型:查找单词 ALL。这意味着它扫描了整个表。
*   桌子:在上面找找类似的东西
*   rows:这些是为了得到结果而遍历的行数
*   filtered:这是经过筛选以得到结果的行数的百分比。
*   额外:这个专栏可能会变得有点大，所以请确保您完整地阅读了它，但是这里寻找临时和文件排序。

它确实提供了更多的信息，但是，这些列将直接指示为什么查询没有正确执行，或者是否可以做些什么来提高查询性能。请记住，行数和筛选的百分比应该一起读取，因为一个对另一个有影响。所有选项都已用尽

假设您尽了最大努力来提高查询性能，现在所有选项都用尽了。除了增强服务器的能力之外，没有什么可做的了。嗯，这将直接依赖于你的应用程序，但也有选择。

以下是当查询优化用尽时可以使用的策略:

*   将查询分成更小的块，并在应用层将它们合并在一起。
*   返回结果的块，直到没有更多的块要返回，并在应用层将这些块合并在一起。与第一个相似，只是方法不同。
*   创建一个 ETL，其中归档的数据可以以预定义的格式存储，然后查询将执行两个操作。一个从 ETL 读取数据，另一个读取实时数据。
*   应用层缓存。类似于 ETL，但是将结果存储在 Redis 实例上。

这里的重要信息是，如果您不能优化查询，那么您必须考虑替代方案。有些情况下，数据太大，不到一秒钟就无法处理。
资源

我可以写到这里结束，但可能无法涵盖查询优化所需的所有内容。这是最基本的，还有很多可以实现的。我发现以下资源对于优化查询至关重要:

*   O'Reilly 关于查询优化的高性能 MySQL 章节深入探讨了查询优化的细微差别以及 MySQL 优化查询的执行方式。
*   MySQL 解释输出文档。不管您运行该命令多少次，这仍然是重要的信息。
*   MySQL 选择优化文档页面。提供了大量关于 MySQL 引擎优化如何执行以及如何利用它的有用信息。

这篇文章最初写于[https://bit.ly/2KJwEED](https://bit.ly/2KJwEED)