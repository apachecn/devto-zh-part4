# 在 LINQ 生成序列号

> 原文：<https://dev.to/karthikchintala/generating-sequence-numbers-in-linq-query-1b01>

在这篇文章中，我们将看到如何在 LINQ C#中生成序列号以及我们需要的数据。

### 在 SQL 中生成序列号

一般来说，我们可以在`SQL`中编写一个类似这样的存储过程，然后在 C#中将它映射到 DTO 或视图模型，这样我们就可以获得序列号和数据。

```
SELECT 
    ROW_NUMBER() OVER (ORDER BY Column1) AS SeqNo, 
    DataColumn1, 
    DataColumn2 
FROM TestTable 
WHERE IsActive = 1 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们使用`Entity Framework`，这是没有任何好处的。让我们看看如何用 C#生成序列号。

### 在 C# LINQ 中生成序列号

首先，我们将使用 DB context 编写查询来获取我们想要的数据。

```
var data = DbContext.TestTable 
                    .Where(a => a.IsActive)
                    .Select(a => new { 
                         DataColumn1 = a.DataColumn1, 
                         DataColumn2 = a.DataColumn2 
                     }); 
```

Enter fullscreen mode Exit fullscreen mode

上面的 LINQ 查询如果执行的话，只是从 TestTable 中提取 Datacolumn1 和 Datacolumn2。现在，要获得另一个带有序列号的列，我们只需枚举上述 LINQ 查询的结果，这样我们就能获得我们想要的列。

幸运的是，`IEnumerable`接口提供了一个重载`SELECT`来完成这个任务。

这里是`SELECT`超载的定义。

```
public static IEnumerable Select<TSource, TResult>(
this IEnumerable source,Func<TSource, int, TResult> selector) 
```

Enter fullscreen mode Exit fullscreen mode

所以，我们来看看是怎么做的。

```
var withSequence = data.AsEnumerable()
                   .Select((a, index) => new 
                      { a.DataColumn1, 
                        a.DataColumn2, 
                        SequenceNo = index + 1 
                       }); 
```

Enter fullscreen mode Exit fullscreen mode

正如您在第 01 行中看到的，我们已经枚举了从 LINQ 到实体的结果，然后我们对枚举集执行了一个`.Select()`。

索引的起始值为零，所以我们将递增它，使索引从 1 开始。这取决于你。

这里就是结果。

<figure>[![](img/50c70dd18e2100fb0f5fd5c28c8cb111.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x8VnsPlc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://coderethinked.com/wp-content/uploads/2020/01/output.png) 

<figcaption>输出</figcaption>

</figure>

### 为什么我们没有在第一次 LINQ 查询中这样做？

第一个 LINQ 查询是`IQueryable`，它没有包含索引的重载`.Select()`。所以，我们必须枚举结果，然后添加序列。因此，为了做到这一点，我们必须首先生成`IQueryable`结果和`IEnumerable`结果(正如我们在使用`.AsEnumerable()`的第二个查询中所做的)。

感谢阅读。

LINQ 查询中[生成序列号的帖子最先出现在](https://coderethinked.com/generating-sequence-numbers-in-linq-query/)[代码重新思考](https://coderethinked.com)上。