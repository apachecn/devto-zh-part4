# SQL Server 动态 SQL 的注意事项

> 原文：<https://dev.to/peledzohar/the-do-s-and-don-ts-of-dynamic-sql-for-sql-server-1bc0>

(这是简短版(虽然包括了所有的项目符号)。一个更长的版本，包括一些文字解释和链接，发布在我的博客上

**该做的事**

*   **始终**将你的标识符列入白名单。
    这可以使用系统表格或视图轻松完成，例如`sys.Tables` + `sys.Column`或`information_Schema.Column`。

*   **总是**使用`sysname`作为标识符的数据类型。
    `sysname`是一种内置的数据类型，相当于 nvarchar(128)，但不可为空。SQL Server 在内部对所有对象名称使用该数据类型。

*   **始终**使用 exec `sp_executeSql`来执行您的动态 SQL(除非它不需要参数，这是一种罕见的情况)。

```
set @sql = N'select col from '+ quotename(@table) N'+ where otherCol = @value'; 

exec sp_executeSql @sql, N'@value varchar(100)', @value 
```

Enter fullscreen mode Exit fullscreen mode

*   **总是**对数据使用参数。

```
set @sql = N'select col from '+ quotename(@table) N'+ where otherCol = @value'; 
-- @table should already be white-listed before you execute the query, so that's O.K 
```

Enter fullscreen mode Exit fullscreen mode

*   **总是**用`quotename`包装标识符。否则，只要您的语句包含一个带空格的标识符(或者许多其他“特殊”字符中的一个)，它就会被中断。

**注意事项**

*   **永远不要**在参数中传递 SQL 命令或子句。

```
set @placeholder1 = 'select a, b, c';
set @placeholder2 = 'where x = y';
set @sql = @placeholder1 + 'from tableName '+ @placeholder2; 
```

Enter fullscreen mode Exit fullscreen mode

*   **永远不要**将参数连接到动态 SQL 字符串中。

```
set @sql = 'select * from table where x = '+ @x 
```

Enter fullscreen mode Exit fullscreen mode

我遗漏了什么吗？如果你认为我做了，请留下评论，这样我可以改进我的帖子。谢谢！