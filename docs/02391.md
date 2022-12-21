# postgexecute.net 不是 ORM

> 原文：<https://dev.to/vbilopav/postgexecute-net-is-not-orm-ljd>

postgexecute.net 是围绕`NpgsqlConnection`对象的轻量级包装器，以方便简单的`PostgreSQL`执行和数据检索。网芯。

**这不是 ORM**

我重复一遍:

**这不是 ORM**

> *   **There is no data conversion** , so there is no impedance mismatch problem.
>     
>     
> *   All read operations will be serialized directly to **`IDictionary<string, object>>`**

或者更好的是:

> *   All read operation versions are called **[λ back to](https://github.com/vbilopav/postgrest.net/tree/master/PostgExecute.Net#read-callback-lambda)** for each row.

这提供了新的灵活性选项。现在您可以:

*   直接序列化为您选择的结构。不需要额外的转换步骤。例如，实例字典的关键字与您的数据库关键字相同。

*   直接写到 stream，json 等...

## 开发者笔记

如果有人希望基于这个代码库构建 micro-ORM `Dapper`风格——他或她可以自由地这么做，只要他们给出适当的致谢/提及和链接到这个库。如果我可以建议一个名字- `NoORM`将是完美的。

当前版本仅适用于`PostgreSQL`，目前没有扩展到其他数据库的计划。

## 用法

*   安装`PostgExecute.Net`或克隆此项目报告。

*   使用该 API 的两种方法:

### 1。`PostgreSQL`连接扩展

`NpgsqlConnection`对象上的扩展(如`Dapper`)。

例如:

```
using (var connection = new NpgsqlConnection("<connection string>"))
{
    // Execute can be chained. Two chained executes under same connection and/or transaction
    connection.Execute("<pgpsql command 1>").Execute("<pgpsql command 2>");

    // ALL methods not returning any results can be chained

    // Execute another command and read some data and return results
    var myResults1 = connection.Execute("<pgpsql command 3>").Read("<pgpsql read 1>");
    // myResults1 is enumerable of row dictionaries

    // Read into dictionary
    var myResults2 = new List<IDictionary<string, object>>();
    connection.Read("<pgpsql read 2>", result => myResults2.Add(result));

    // Read into custom class
    var myResults3 = List<MyResults>();
    connection.Read("<pgpsql read 3>", r => myResults3.Add(new MyResults{ Field1 = result["field1"] }));

    // etc..
} 
```

### 2。静态方法

每个扩展都有与静态方法完全相同的版本，静态方法采用第一个参数连接字符串。

例如:

```
Postg.Execute("<connection string>", "pgpsql command 1");
Postg.Execute("<connection string>", "pgpsql command 2");
// ... 
```

**备注:**

> 没有可用的链接，因为连接只使用了一次并立即被处理。
> 
> **使用静态方法时`PostgreSQL`新的连接将被创建和处理。**
> 
> `NpgsqlConnection`将回收来自同一线程的连接(它们将具有相同的 PID)，但是，任何挂起的事务都将丢失并回滚。

### API

所有可用 API 及其重载的完整列表可以在[接口定义](https://github.com/vbilopav/postgrest.net/blob/master/PostgExecute.Net/IPostg.cs)中找到。

它们分为以下三类:

1.  `Execute`和`ExecuteAsync` -在`PostgreSQL`数据库上执行 PGPSQL 命令

2.  `Single`和`SingleAsync` -从`PostgreSQL`数据库中提取单行

3.  `Read`和`ReadAsync` -从`PostgreSQL`数据库中读取多行

每个版本都有以`Async`后缀结尾的`async`版本，它们以相同的方式处理参数:

### 用参数工作

> 按照惯例，查询中的参数必须以字母`@`开头

#### 位置参数

示例:

```
 var result = connection.Single(
    @"select * from (
        select 1 as first, 'foo' as bar, '1977-05-19'::date as day, null as null
    ) as sub
    where first = @1 and bar = @2 and day = @3
    ",
    1, "foo", new DateTime(1977, 5, 19)); 
```

**注:**

> 位置参数按照查询中出现的顺序赋值**,**名称完全不相关**(但是，它们必须有一个)。**

#### 命名参数

与位置参数不同，当使用命名参数时，位置是不相关的，每个参数**必须有唯一的名称。**

接受命名参数接口公开参数集合(类型 [`NpgsqlParameterCollection`](https://github.com/npgsql/npgsql/blob/dev/src/Npgsql/NpgsqlParameterCollection.cs) ) -作为 lambda 参数:

```
var result = connection.Single(
    @"select * from (
        select 1 as first, 'foo' as bar, '1977-05-19'::date as day, null as null
    ) as sub
    where first = @1 and bar = @2 and day = @3
    ", p => {
        p.AddWithValue("3", new DateTime(1977, 5, 19));
        p.AddWithValue("2", "foo");
        p.AddWithValue("1", 1);
    }); 
```

这个 API 还定义了参数集合类型的扩展——允许链接。这允许 muc mroe 优雅的语法:

```
var result = connection.Single(
    @"select * from (
        select 1 as first, 'foo' as bar, '1977-05-19'::date as day, null as null
    ) as sub
    where first = @1 and bar = @2 and day = @3

    ", p => p.Add("3", new DateTime(1977, 5, 19)).Add("2", "foo").Add("1", 1)); 
```

还有一个更短的别名叫做`@P` :

```
var result = connection.Single(
    @"select * from (
        select 1 as first, 'foo' as bar, '1977-05-19'::date as day, null as null
    ) as sub
    where first = @1 and bar = @2 and day = @3

    ", p => p.@P("3", new DateTime(1977, 5, 19)).@P("2", "foo").@P("1", 1)); 
```

API 也可以采用这个λ的`async`版本:

```
var result = connection.Single(
    @"select * from (
        select 1 as first, 'foo' as bar, '1977-05-19'::date as day, null as null
    ) as sub
    where first = @1 and bar = @2 and day = @3

    ", async p => {
        await Task.Delay(0); // some async operation...
        p.@P("3", new DateTime(1977, 5, 19)).@P("2", "foo").@P("1", 1);
    }); 
```

### 获取数据

该 API 的默认结果集是每行的`IDictionary<string, object>`:

```
var result = connection.Single("select 1, 'foo' as bar, '1977-05-19'::date as day, null as null");
// result is `IDictionary<string, object>` that represent result row. 
```

空结果集将产生空字典。

**注:**

> 没有任何数据转换(这不是 ORM)。
> 
> `null`值将**不是**有`C#``null`——而是`DBNull.Value`代替。

出现这种情况的原因是因为你的`null`和数据库`null` **不是一回事。**

相同的逻辑适用于读取多行。读取将返回`IEnumerable<IDictionary<string, object>>` :

```
var result = connection.Read(
    @"select * from (
    values
        (1, 'foo1', '1977-05-19'::date),
        (2, 'foo2', '1978-05-19'::date),
        (3, 'foo3', '1979-05-19'::date)
    ) t(first, bar, day)");

// result is `IEnumerable<IDictionary<string, object>>` 
```

### 读回调λ

每个 read 方法都有接受 lambda 回调的版本，该回调为每一行执行:

```
var results = new List<IDictionary<string, object>>();
await connection.Read(
    @"select * from (
    values
        (1, 'foo1', '1977-05-19'::date),
        (2, 'foo2', '1978-05-19'::date),
        (3, 'foo3', '1979-05-19'::date)
    ) t(first, bar, day)",
    result => results.Add(result)); 
```

每个版本也有 lambda 重载，这些重载将`bool`作为返回值。

在这种情况下，您可以返回`false`来立即安全地中断迭代:

```
var results = new List<IDictionary<string, object>>();
connection.Read(
    @"select * from (
    values
        (1, 'foo1', '1977-05-19'::date),
        (2, 'foo2', '1978-05-19'::date),
        (3, 'foo3', '1979-05-19'::date)
    ) t(first, bar, day)",
    result =>
    {
        if ((int)result["first"] == 2)
        {
            return false;
        }
        results.Add(r);
        return true;
    });
// breaks on second row, third is never executed, result will have only one entry 
```

当然，每个 API 版本都有`async`重载:

```
await connection.ReadAsync(@"
        select * from (
        values
            (1, 'foo1', '1977-05-19'::date),
            (2, 'foo2', '1978-05-19'::date),
            (3, 'foo3', '1979-05-19'::date)
        ) t(first, bar, day)",
async result =>
{
    await Task.Delay(0); // some async operation...
    results.Add(result);
}); 
```

## 测试

测试覆盖率为 100%，可以在这里找到

## 未来的计划

当`C# 8.0`最终出现时——为 C# 8.0 支持的读操作实现那些奇特的、超快的异步流(例如`async return yield`