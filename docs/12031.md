# 数据库视图与表函数

> 原文：<https://dev.to/chochos/database-views-vs-table-functions-2npm>

视图是关系数据库的一个非常有用的特性。您可以定义一个复杂的查询并将其存储为视图，然后像查询表一样查询该视图。

我有一个复杂的查询，涉及跨 4 个表的连接。这是很多列。所以我创建了一个视图来简化事情，它工作得非常好。除此之外，有一天我们不得不改变一个列的类型，因为我们搞砸了，它要求我们删除视图。情况类似于这样:

```
--A simple table as an example
CREATE TABLE example(
  table_id BIGSERIAL PRIMARY KEY,
  created  TIMESTAMP,
  other_stuff VARCHAR(20)
);

--An example of a view, as a simple query to the table
CREATE VIEW example_view AS SELECT * FROM example;

--If we try to modify the table, we get an error
ALTER TABLE example ALTER COLUMN created TYPE TIMESTAMP WITH TIME ZONE;

--We need to drop the view so we can modify the table
DROP VIEW example_view;

--Then we can alter it
ALTER TABLE example ALTER COLUMN created TYPE TIMESTAMP WITH TIME ZONE;

--Finally, re-create the view
CREATE VIEW example_view AS SELECT * FROM example; 
```

在我的例子中，我们需要修改的表有超过 800 万行，所以`ALTER TABLE`命令需要几秒钟才能运行，在此期间没有视图，所以依赖于该视图的所有功能都必须脱机。

修改表之后，创建视图根本不需要时间，但是表修改期间的停机时间是我想要消除的不便。所以我开始寻找替代品。

### 表格功能

在阅读 PostgreSQL 文档时，我遇到了*表函数*。这些函数返回类似表格的结构，您可以像查询表格一样查询它们。按照前面的示例，我们可以创建一个表函数来代替视图，如下所示:

```
CREATE FUNCTION example_function()
  --The return type must include column names and types, like a table definition
  RETURNS TABLE(table_id BIGINT, created TIMESTAMP, other_stuff VARCHAR) AS
$$
BEGIN
  RETURN QUERY SELECT * FROM example;
END
$$
language plpgsql; 
```

然后我们可以像这样查询它:

```
SELECT * FROM example_function(); 
```

如果我们以后决定更改该表，我们可以这样做而不会出现错误。**但是**，下一次我们查询表函数时**会**得到一个错误，因为表结构不再匹配函数的返回类型。因此，我们仍然有一个问题，即在更改表后查询会停止工作。

### 一点元编程

解决这个问题的一种方法是使用一点元编程。我们可以不直接创建表函数，而是创建一个函数来创建表函数，从表的元数据动态定义返回类型。这样，当我们修改表时，只需再次运行生成器函数，表函数就会用新的表元数据更新。大概是这样的:

```
CREATE FUNCTION create_table_function() RETURNS INT AS
$META$
DECLARE
  columns TEXT;
  cmd     TEXT;
BEGIN
  --Here we create a string with all the column names and their types
  --separated by commas, to use as the return type of the function
  SELECT (SELECT STRING_AGG(column_name || ' ' || data_type, ', ')
    from information_schema.columns where table_name = 'example'
    group by table_name) INTO columns;

  --Here we create the whole function definition
  SELECT 'CREATE FUNCTION example_function() RETURNS TABLE(' ||
    columns || ') AS $$ BEGIN ' ||
    'RETURN QUERY SELECT * FROM example; END ' ||
    '$$ language plpgsql;'
    INTO cmd;
  --First we need to drop the function, because we can't replace
  --an existing one due to the different return type
  DROP FUNCTION IF EXISTS example_function();
  EXECUTE cmd;
  RETURN 1;
END
$META$
language plpgsql; 
```

上面的函数创建创建`example_function`的命令，从`example`表元数据构造返回类型。当我们调用这个生成器函数时，它会创建我们需要的表函数。所以在修改了表格之后，我们只需要再次调用这个函数，表格函数就会立即更新。嗯，*几乎是*瞬间。

还有一个小问题:如果有人改变了我们的表函数所使用的表中的一列，而忘记了重新生成表函数本身，该怎么办？许多错误将开始出现。解决这个问题的一种方法是让表函数执行的查询将每一列都转换为函数的返回类型中定义的类型。这需要对表函数进行一些调整，再次使用表的元数据:

```
CREATE FUNCTION create_table_function() RETURNS INT AS
$META$
DECLARE
  columns TEXT;
  query   TEXT;
  cmd     TEXT;
BEGIN
  --This remains the same
  SELECT (SELECT STRING_AGG(column_name || ' ' || data_type, ', ')
    from information_schema.columns where table_name = 'example'
    group by table_name) INTO columns;

  --Now we create a string with each column, casting it to the type
  --it currently has, so it always matches the return type of the function
  SELECT (SELECT STRING_AGG('CAST(example.' || column_name || ' AS ' || data_type, '), ')
    from information_schema.columns where table_name = 'foo'
    group by table_name) INTO query;

  --we create the function with the new query
  SELECT 'CREATE FUNCTION example_function() RETURNS TABLE(' ||
    columns || ') AS $$ BEGIN ' ||
    --we need a closing parens because of the way STRING_AGG works
    'RETURN QUERY SELECT ' || query || ') FROM example;' ||
    'END $$ language plpgsql;'
    INTO cmd;
  DROP FUNCTION IF EXISTS example_function();
  EXECUTE cmd;
  RETURN 1;
END
$META$
language plpgsql; 
```

因为每个列都被转换为创建 table 函数时表所具有的数据类型，所以更改这些列中的一个不会影响函数的返回类型，所以现在函数根本不受修改的影响(除非从表中删除一列，但这也是视图的一个问题)。表格功能可以在以后更新。

### 一切都是有得有失

当然，这种方法也有缺点。最明显的一点是，与创建视图的简单性相比，动态创建表函数很笨拙。

然而，一个很大的缺点是你不能索引表函数；我发现的绕过这一限制的唯一方法是每个查询有一个函数，或者向函数添加参数，以便它可以根据传递给它的参数执行不同的查询。

在任何情况下，改变表列的类型都不是您应该经常做的事情。最常见的情况是扩展 VARCHAR 列，这不会影响表函数(但会影响视图，所以这是表函数相对于视图的另一个优势)。

我并不是在每种情况下都提倡使用表函数而不是视图，但是我认为，如果您发现自己处于需要修改视图中包含的表的情况，并且需要做一些事情来最小化停机时间，这是一个非常有用的技巧。