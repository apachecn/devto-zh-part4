# Postgres 三元模型指数 VS Algolia

> 原文：<https://dev.to/saashub/postgres-trigram-indexes-vs-algolia-1oma>

我想分享我使用 Postgres 的原生“三元组索引”实现强大的“提前键入/自动完成/模糊搜索”功能的经验。我用 Algolia 作为一个例子，因为我注意到许多人正是为此而使用它。然而，我相信在许多情况下，只使用 Postgres 也可以做得很好。

什么是三元模型索引？它们是 Postgres 中一种特殊类型的索引，默认情况下可用；但是，必须显式启用它们。以下是来自官方文件的摘录:

*" pg _ trgm 模块提供了基于三元组匹配确定字母数字文本相似性的函数和运算符，以及支持快速搜索相似字符串的索引运算符类。"“三元模型是从字符串中提取的一组三个连续的字符。”*

*例如，字符串“cat”中的三元组是“c”、“ca”、“cat”和“at”。字符串“foo|bar”中的三元组是“f”、“fo”、“foo”、“oo”、“b”、“ba”、“bar”和“ar”。*

现在让我们来看看构建自动完成引擎需要做些什么。

## 1)启用 Postgres 扩展

`CREATE EXTENSION IF NOT EXISTS pg_trgm`

## 2)索引你要查询的列

一旦你启用了`pg_trgm`，你必须索引`varchar`和`text`列来使用它。如`CREATE INDEX index_fuzzy_searches_on_name_trgm ON fuzzy_searches USING gin(name gin_trgm_ops)`。注意**使用 gin(名称 gin_trgm_ops)**

现在我们有了非常快速的`LIKE`查询，带有前导和尾随通配符(`%`)。

## 3)准备查询

下一步是按空格分割用户查询，并为每个名字添加一个单独的 **WHERE** 子句。例如，如果用户搜索“销售云”。我们将以类似于`SELECT * FROM mytable WHERE name LIKE '%sales%' AND name LIKE '%cloud%'`的查询结束

Ruby on Rails 中的一个简化实现可能是这样的(在这种情况下，`ServiceSearch`将是物化视图，我将在后面的文章中解释):

```
relation = ServiceSearch.popular_first.limit(limit).includes(:service)
query.split("\s").each do |word|
  relation = relation.where("content LIKE ?", "%#{word}%")
end
relation.map(&:service) 
```

Enter fullscreen mode Exit fullscreen mode

## 4)同时搜索不同类型的记录

使用专用全文搜索引擎的一个用例是同时查询所有记录。这可以通过一个物化视图来实现。而且，你可以很容易地优先考虑一些记录。例如，在类别之前显示产品。我正在通过令人敬畏的[风景宝石](https://ruby.libhunt.com/scenic-alternatives)管理我的观点，但是你不需要。

这里是示例性物化视图的主体:

```
SELECT
  id AS searchable_id,
  'Service' AS searchable_type,
  1 AS priority,
  post_services_count AS weight,
  LOWER(name) AS name
FROM services
WHERE state IN ('approved', 'closed')

UNION

SELECT
  categories.id AS searchable_id,
  'Category' AS searchable_type,
  2 AS priority,
  COUNT(*) AS weight,
  LOWER(name) AS name
FROM categories
JOIN categorizations cats ON cats.category_id = categories.id
WHERE state = 'approved'
GROUP BY categories.id, categories.name 
```

Enter fullscreen mode Exit fullscreen mode

通过这样一个视图，我们可以用简单的方式查询多条记录:

```
SELECT * FROM fuzzy_searches WHERE name LIKE '%test%' ORDER BY priority, weight DESC 
```

Enter fullscreen mode Exit fullscreen mode

## 真实世界的例子

SaaSHub-【alternativeto.net】T2 的主要竞争对手之一正在使用 Algolia 进行搜索自动完成。我建议测试他们和 SaaSHub 的搜索。你不会发现 UX 和速度有太多的不同。

最后，如果您的项目需要一个简单的自动完成功能，并且您正在使用 Postgres，我的建议是考虑它的**三元组**索引+物化视图。寻求外部服务可能没有必要，而且可能是一种矫枉过正。然而，我相信在很多情况下，购买专业的 SaaS 产品是有意义的。

* * *

另外，你可以在 SaaSHub 上找到更多的 Algolia 替代品🙈。