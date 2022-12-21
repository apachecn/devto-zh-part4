# PostgreSQL:如何更新和删除数据

> 原文：<https://dev.to/miku86/postgresql-how-to-update-delete-data-gac>

## [T1】简介](#intro)

所以我们学习了[如何创建简单的查询](https://dev.to/miku86/postgresql-how-to-read-from-write-to-our-table-48kg)来读取&写一些数据。

我们还学习了[如何过滤&排序我们的数据](https://dev.to/miku86/postgresql-how-to-create-some-basic-queries-57l8)。

现在我们想学习如何更新和删除数据。

* * *

## 连接数据库外壳

首先，我连接到我创建的数据库 shell:

```
psql -d miku86-db 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 读取:显示`person`-表格中的所有数据

然后，我检查我当前的表:

```
miku86-db=# SELECT * FROM person;
 id | nickname | country | available 
---------+----------+---------+-----------
  1 | miku86   | germany | t
  2 | heidi    | austria | t
  3 | tom      | usa     | f
  4 | pavel    | russia  | f
(4 rows) 
```

Enter fullscreen mode Exit fullscreen mode

I `SELECT`所有(`*` =通配符)行`FROM``person`-表。
里面有`4 rows`。

* * *

## 删除一行

简化语法:

```
DELETE FROM [table] WHERE [expression]; 
```

Enter fullscreen mode Exit fullscreen mode

### 例#1:删除`1`中`id`的人:

```
miku86-db=# DELETE FROM person WHERE id = 1;
DELETE 1 
```

Enter fullscreen mode Exit fullscreen mode

在`DELETE`-查询下面可以看到`DELETE 1`。
命令执行成功，`1`行被删除。

```
miku86-db=# SELECT * FROM person;
 id | nickname | country | available 
---------+----------+---------+-----------
  2 | heidi    | austria | t
  3 | tom      | usa     | f
  4 | pavel    | russia  | f
(3 rows) 
```

Enter fullscreen mode Exit fullscreen mode

`1`的`id`的人被删除。

* * *

### 例 2:删除没空的人(= >有空=假):

```
miku86-db=# DELETE FROM person WHERE available = false;
DELETE 2 
```

Enter fullscreen mode Exit fullscreen mode

在`DELETE`-查询下面可以看到`DELETE 2`。
命令执行成功，`2`行被删除。

```
miku86-db=# SELECT * FROM person;
 id | nickname | country | available 
---------+----------+---------+-----------
  2 | heidi    | austria | t
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

例 1 中删除了`1`的`id`的人。
本例中删除了`f` (=假)中带有`available`的人员。

* * *

## 更新一行

简化语法:

```
UPDATE [table] SET [col] = [newvalue] WHERE [expression]; 
```

Enter fullscreen mode Exit fullscreen mode

### 例#1:将`austria`的`country`更新为`germany`的`country`:

```
miku86-db=# UPDATE person SET country = 'germany' WHERE country = 'austria';
UPDATE 1 
```

Enter fullscreen mode Exit fullscreen mode

在`UPDATE`-查询下面可以看到`UPDATE 1`。
命令执行成功，`1`行被更新。

```
miku86-db=# SELECT * FROM person;
 id | nickname | country | available 
---------+----------+---------+-----------
  2 | heidi    | germany | t
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

`austria`的`country`的人更新为`germany`的`country`。

* * *

## 下一部分

我们将学习如何连接两个表。

* * *

## 进一步阅读

[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[删除文档](https://www.postgresql.org/docs/current/sql-delete.html)
[更新文档](https://www.postgresql.org/docs/current/sql-update.html)