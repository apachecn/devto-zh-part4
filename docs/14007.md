# 如何在 SQL 中编写可选过滤器

> 原文：<https://dev.to/tiim/how-to-write-optional-filters-in-sql-fei>

## 问题

假设您有一个 rest API，它具有以下端点，可以返回数据库中的所有书籍:

```
GET /book/ 
```

Enter fullscreen mode Exit fullscreen mode

您的 SQL 查询可能如下所示

```
SELECT * 
FROM books 
```

Enter fullscreen mode Exit fullscreen mode

例如，有时您只想列出某个特定作者的书籍。我们如何在 SQL 中做到这一点？

## 天真解:字符串串联✂

一种方法是连接您的 sql 查询，如下所示:

```
const arguments = [];
const queryString = 'SELECT * FROM books WHERE true';
if ( authorFilter != null) {
  queryString  += 'AND author = ?';
  arguments.push(authorFilter);
}
db.query(queryString, arguments); 
```

Enter fullscreen mode Exit fullscreen mode

我不太喜欢手动连接字符串。

## 聚结功能🌟

大多数数据库都有函数`coalesce`，它接受数量可变的参数，并返回第一个不为空的参数。

```
-- Examle
SELECT coalesce(null, null, 'dev.to', null, '@TiimB') as example;

-- Will return 

example
---------
dev.to 
```

Enter fullscreen mode Exit fullscreen mode

但是这个函数将如何帮助我们呢？

## 带合并功能的可选过滤器

```
SELECT * 
FROM books
WHERE 
  author = coalesce(?, author); 
```

Enter fullscreen mode Exit fullscreen mode

如果过滤器值为空，合并表达式将解析为`author`
，比较结果`author = author`将为真。

另一方面，如果值被设置为例如莎士比亚，那么作者将被与莎士比亚进行比较。

我只是最近才发现这种实现可选过滤器的方法。如果你有一个更习惯的方法来做到这一点，请让我知道✨

如果你喜欢这篇文章，请在这里或 Twitter 上关注我，关注标题为 [@TiimB](https://twitter.com/TiimB) 😎