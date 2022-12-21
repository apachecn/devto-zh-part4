# 在 PostgreSQL、MySQL 和其他关系数据库中的 JSON 上运行 SQL 的方法

> 原文：<https://dev.to/rocksetcloud/methods-for-running-sql-on-json-in-postgresql-mysql-and-other-relational-databases-4p15>

从我们的数据中获取价值的主要障碍之一是我们必须将数据转换成可供分析的形式。这听起来很简单，但事实很少如此。想想我们在 PostgreSQL 和 MySQL 等关系数据库中处理半结构化数据(如 JSON)时必须经历的困难。

### 关系数据库中的 JSON

在过去，当涉及到使用 JSON 数据时，我们不得不在与 JSON 配合良好的工具和平台或者为分析提供良好支持的工具之间进行选择。JSON 很适合文档数据库，比如 MongoDB。对于关系数据库来说，这并不是一个很好的匹配(尽管许多已经实现了 JSON 函数和类型，我们将在下面讨论)。

在软件工程术语中，这就是所谓的高阻抗不匹配。关系数据库非常适合具有相同属性的一致结构化数据，这些数据一行接一行地反复出现。另一方面，JSON 非常适合于捕获不同内容和结构的数据，并且已经成为一种非常常见的数据交换格式。

现在，考虑将 JSON 数据加载到关系数据库中需要做什么。第一步是理解 JSON 数据的模式。这从识别文件中的所有属性并确定它们的数据类型开始。一些数据类型，比如整数和字符串，可以很好地从 JSON 映射到关系数据库数据类型。

其他数据类型需要更多的思考。例如，日期可能需要重新格式化或转换为日期或日期时间数据类型。

复杂的数据类型，如数组和列表，不能直接映射到本地的关系数据结构，所以需要更多的努力来处理这种情况。

### 方法一:将 JSON 映射到表结构

我们可以使用数据库内置的 JSON 函数将 JSON 映射到一个表结构中。例如，假设一个名为`company_regions`的表维护着包括一个`id`、`region`和`country`的元组。可以使用 PostgreSQL 中的[内置`json_populate_record`函数插入一个 JSON 结构，如下例:](https://www.postgresql.org/docs/11/functions-json.html) 

```
INSERT INTO company_regions
   SELECT * 
   FROM json_populate_record(NULL::company_regions,      
             '{"region_id":"10","company_regions":"British Columbia","country":"Canada"}') 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的优点是，我们获得了关系数据库的全部好处，例如使用 SQL 查询的能力，以及与查询结构化数据相当的性能。主要缺点是，我们必须投入额外的时间来创建提取、转换和加载(ETL)脚本来加载这些数据——这是我们可以分析数据而不是转换数据的时间。此外，复杂的数据，如数组和嵌套，以及意外的数据，如特定属性的字符串和整数类型的混合，都会给 ETL 管道和数据库带来问题。

### 方法二:在表列中存储 JSON

另一种选择是将 JSON 存储在表列中。这个特性在一些关系数据库系统中是可用的——[PostgreSQL](https://www.postgresql.org/docs/11/datatype-json.html)和 [MySQL](https://dev.mysql.com/doc/refman/8.0/en/json.html) 支持 JSON 类型的列。

例如，在 PostgreSQL 中，如果一个名为`company_divisions`的表有一个名为`division_info`的列，并以`{"division_id": 10, "division_name":"Financial Management", "division_lead":"CFO"}`的形式存储 JSON，那么可以使用`->>`操作符查询该表。比如:

```
SELECT 
    division_info->>'division_id' AS id,
    division_info->>'division_name' AS name,
    division_info->>'division_lead' AS lead
FROM 
    company_divisions 
```

Enter fullscreen mode Exit fullscreen mode

如果需要，我们还可以在 JSON 列中的数据上创建[索引](https://www.postgresql.org/docs/current/datatype-json.html#JSON-INDEXING)，以加快 PostgreSQL 中的查询速度。

这种方法的优点是需要较少的 ETL 代码来转换和加载数据，但是我们失去了关系模型的一些优点。我们仍然可以使用 SQL，但是查询和分析 JSON 列中的数据会比我们将它转换成具有本机类型的表结构时性能更差，因为缺少统计信息和索引效率更低。

### 一个更好的选择:基于全索引 JSON 的标准 SQL

在 JSON 上实现 SQL 分析有一个更自然的方法。我们可以使用 SQL 直接查询 JSON 数据，而不是试图将自然适合 JSON 的数据映射到关系表中。

Rockset 按原样索引 JSON 数据，并为最终用户提供一个查询数据的 SQL 接口，以支持应用程序和仪表板。

[![json-sql-rockset](img/e6d175eb40ae8324775b20e8c669f8ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--51qd1AcZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/1d31s1aajogl/13u5HqrP8KpfNulfyGKn9F/122cacf493d5981b26e5458599525e11/json-sql-rockset.png)

当新数据到达数据源时，它会不断地对其进行索引，因此不会出现查询的数据与数据源不同步的情况。另一个好处是，因为 [Rockset 不需要固定的模式](https://rockset.com/blog/dynamic-typing-in-sql/)，所以即使模式发生变化，用户也可以继续从数据源获取和索引。

获得的效率是显而易见的:我们摆脱了繁琐的 ETL 代码，最小化了我们的数据管道，并利用自动生成的所有数据的索引来获得更好的查询性能。