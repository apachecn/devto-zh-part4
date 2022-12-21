# CSV、LTSV、JSON 和 TBLN 上的 SQL。

> 原文：<https://dev.to/petercour/sql-on-csv-ltsv-json-and-tbln-n36>

您可以运行 CSV、LTSV、JSON 和 TBLN 格式的 SQL 查询。为此，您可以使用一个名为 [trdsql](https://github.com/noborus/trdsql) 的 golang 程序

这些文件只是文件，但是使用这个工具，查询语言 SQL 就变得可用了。SQL 常用于查询数据库。

例如:

```
$ trdsql "SELECT * FROM test.csv" 
```

在这种情况下，csv 文件是一个单独的数据库表。它还支持 ASCII 输出:

```
$ trdsql -oat "SELECT * FROM test.csv"

+----+--------+
| C1 |   C2   |
+----+--------+
|  1 | Orange |
|  2 | Melon  |
|  3 | Apple  |
+----+--------+ 
```

整洁对吗？

参见 gif:

[![](img/601e74bfcb4ede02840fbd76b6865b4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dLzOxVyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/noborus/trdsql/master/doc/trdsql.gif)

学习 golang:

*   [https://golangr.com](https://golangr.com)
*   [https://golang.org/doc/install](https://golang.org/doc/install)