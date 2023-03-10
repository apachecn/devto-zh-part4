# 带限制/偏移量的服务器端分页

> 原文：<https://dev.to/stephencweiss/server-side-pagination-with-limit-offset-296e>

如何限制查询返回的行数？

让我们考虑一个成员表的基本例子。

您正在构建一个一次显示十个成员的前端。你有 1000 名成员。

有时，您将只筛选名字以 S 开头的成员，但大多数情况下，您只需要整个列表。

如果您查询整个表，并将其发送给客户端，那么您就可以使用 Javascript 对结果进行切片、切割和过滤。

然而，这并不是非常高效，虽然 1，000 条记录可能不成问题，但是 1，000，000 条或 1，000，000，000 条记录会怎么样呢？在某一点上，数字变得足够大，以至于加载时间膨胀，用户坐着，而旋转器一圈又一圈地旋转。

什么是解决方案？我们不用查询*整个*表并将结果发送给客户端，而是可以使用`LIMIT`和`OFFSET`来获得每页的有限结果集(这将控制偏移量)。

在 select 语句中使用`LIMIT`和`OFFSET`:

```
SELECT select_list
    FROM table_expression
    [ORDER BY …]
    [LIMIT { number | ALL }] [OFFSET number] 
```

例如，要从`my_table`中选择第 101 到第 110 条最近修改的记录，查询将是:

```
SELECT * FROM my_table ORDER BY date_modified DESC LIMIT 10 OFFSET 100; 
```

买者自负:无论何时使用`LIMIT` / `OFFSET`，如果一致的结果是期望的，使用`ORDER BY`条款强制执行。从文档中:

> 查询优化器在生成查询计划时会将`LIMIT`考虑在内，因此根据您为`LIMIT`和`OFFSET`提供的内容，您很可能会得到不同的计划(产生不同的行顺序)。因此，使用不同的`LIMIT` / `OFFSET`值来选择查询结果的不同子集将会产生不一致的结果，除非您使用`ORDER BY`强制执行可预测的结果排序。

虽然理论上这两个属性都可以由客户端灵活处理，但是出于分页的目的，必需的*属性是`OFFSET` ( `LIMIT`可以被调整以改变页面上显示的记录数量，但是除非您使用*所有的*记录/移除限制，否则`OFFSET`仍然是需要的)。*

记住这一点，我们可以使用服务`getRecords`生成查询，如下所示:

```
public async getRecords(opts) {
  const query = SQL`
    select * from my_table
    where true
  `;
  const sortOrder = opts.sortOrder || “ date_modified”;
    const limit = opts.limit || 10;
  const offset = opts.offset || 0;
  query.append(` order by ${sortOrder}`);
  query.append(` limit ${opts.limit}`);
    query.append(` offset ${opts.offset}`);

  const { rows } = await this._pool.query(query);
  return rows;
} 
```

现在，我们有了一种从客户端设置分页的动态方法，客户端不会收到大量不需要的记录。

回到我们最初的问题:我们现在知道如何通过使用`LIMIT`和`OFFSET`来限制查询返回的行数。我们知道包含一个`ORDER BY`条款以保持一致性是很重要的。我们还看到了如何生成查询来实现服务器端分页。

# 脚注:

*   <sup>1</sup> [限制和偏移量| PostgreSQL](https://www.postgresql.org/docs/current/queries-limit.html)