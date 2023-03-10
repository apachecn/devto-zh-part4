# PostgreSQL:如何创建一些基本查询

> 原文：<https://dev.to/miku86/postgresql-how-to-create-some-basic-queries-57l8>

## [T1】简介](#intro)

所以我们学习了[如何创建简单的查询](https://dev.to/miku86/postgresql-how-to-read-from-write-to-our-table-48kg)来读取&写一些数据。

现在我们想看看创建一些基本查询的一些附加方法。

* * *

## 连接数据库外壳

首先，我连接到我创建的数据库 shell:

```
psql -d miku86-db 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 读取:再次显示`person`-表格中的所有数据；

*   我创建了一个新的`person`-表来包含更多的列:

```
miku86-db=# CREATE TABLE person (id SERIAL PRIMARY KEY, nickname TEXT NOT NULL, country TEXT, available BOOLEAN NOT NULL); 
```

Enter fullscreen mode Exit fullscreen mode

```
miku86-db=# \d person
                              Table "public.person"
  Column   |  Type   | Collation | Nullable |              Default               
----------------+---------+-----------+----------+------------------------------------
 id        | integer |           | not null | nextval('person_id_seq'::regclass)
 nickname  | text    |           | not null | 
 country   | text    |           |          | 
 available | boolean |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id) 
```

Enter fullscreen mode Exit fullscreen mode

*   我添加了一些行:

```
miku86-db=# INSERT INTO person(nickname, country, available) VALUES ('miku86', 'germany', true);
miku86-db=# INSERT INTO person(nickname, country, available) VALUES ('heidi', 'austria', true);
miku86-db=# INSERT INTO person(nickname, country, available) VALUES ('tom', 'usa', false);
miku86-db=# INSERT INTO person(nickname, country, available) VALUES ('pavel', 'russia', false); 
```

Enter fullscreen mode Exit fullscreen mode

*   显示所有行:

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

## 显示具体的栏目

有时我们想只显示特定的列。

### 例#1:显示所有人，但只显示`nickname`和`country`列:

```
miku86-db=# SELECT nickname, country FROM person;
 nickname | country 
---------------+---------
 miku86   | germany
 heidi    | austria
 tom      | usa
 pavel    | russia
(4 rows) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## `WHERE`从句

有时我们只想显示特定的结果。
这就是 [`WHERE`从句](https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-WHERE)派上用场的地方。

```
SELECT * FROM [table] WHERE [expression]; 
```

Enter fullscreen mode Exit fullscreen mode

### 例#1:显示`1`的`id`的人:

```
miku86-db=# SELECT * FROM person WHERE id = 1;
 id | nickname | country | available 
---------+----------+---------+-----------
  1 | miku86   | germany | t
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，表达式是`id = 1`。

### 例#2:显示没空的人(= >有空=假):

```
miku86-db=# SELECT * FROM person WHERE available = false;
 id | nickname | country | available 
---------+----------+---------+-----------
  3 | tom      | usa     | f
  4 | pavel    | russia  | f 
```

Enter fullscreen mode Exit fullscreen mode

注意:布尔接受多种表示，例如`'f'`、`'no'`、`'0'`。[阅读文件](https://www.postgresql.org/docs/current/datatype-boolean.html)。

* * *

## `LIMIT`从句

有时候我们想限制结果。
这就是 [`LIMIT`从句](https://www.postgresql.org/docs/current/sql-select.html#SQL-LIMIT)派上用场的地方。

### 例#1:显示所有人，但最多 2:

```
miku86-db=# SELECT * FROM person LIMIT 2;
 id | nickname | country | available 
---------+----------+---------+-----------
  1 | miku86   | germany | t
  2 | heidi    | austria | t
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

如果没有`LIMIT 2`，我们将看到 4 行，但是我们将其限制为 2 行。

* * *

## `ORDER BY`

有时我们希望对结果进行排序。
这就是 [`ORDER BY`文档](https://www.postgresql.org/docs/current/queries-order.html)派上用场的地方。

### 例#1:显示所有人并按国家排序(升序):

```
miku86-db=# SELECT * FROM person ORDER BY country;
 id | nickname | country | available 
---------+----------+---------+-----------
  2 | heidi    | austria | t
  1 | miku86   | germany | t
  4 | pavel    | russia  | f
  3 | tom      | usa     | f
(4 rows) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 下一部分

我们将学习如何更新和删除数据。

* * *

## 进一步阅读

[PostgreSQL Docs](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[PostgreSQL 数据类型](https://www.postgresql.org/docs/current/datatype.html)
[选择-列表项 Docs](https://www.postgresql.org/docs/current/queries-select-lists.html#QUERIES-SELECT-LIST-ITEMS)
[`WHERE`Docs](https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-WHERE)
[T1】Docs](https://www.postgresql.org/docs/current/sql-select.html#SQL-LIMIT)
[Docs](https://www.postgresql.org/docs/current/queries-order.html)