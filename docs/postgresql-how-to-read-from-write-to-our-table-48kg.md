# PostgreSQL:如何读写我们的表

> 原文：<https://dev.to/miku86/postgresql-how-to-read-from-write-to-our-table-48kg>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了](https://dev.to/miku86/postgresql-what-is-it-how-to-install-it-16hk)和 [setup](https://dev.to/miku86/postgresql-how-to-setup-our-installed-postgresql-39hf) PostgreSQL。

我们还学习了如何创建一个表格。

现在我们想学习如何从我们的表中读取和写入。

* * *

## 连接数据库外壳

首先，我连接到我创建的数据库 shell:

```
psql -d miku86-db 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
miku86-db=# 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 检查表

*   显示数据库的表格:

```
miku86-db=# \dt
         List of relations
 Schema |  Name  | Type  |  Owner   
-------------+--------+-------+----------
 public | person | table | miku86
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

*   显示`person`表的概述:

```
miku86-db=# \d person
                             Table "public.person"
  Column  |  Type   | Collation | Nullable |              Default               
---------------+---------+-----------+----------+------------------------------------
 id       | integer |           | not null | nextval('person_id_seq'::regclass)
 nickname | text    |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 读取:显示`person`-表格中的所有数据；

```
miku86-db=# SELECT * FROM person;
 id | nickname 
---------+----------
(0 rows) 
```

Enter fullscreen mode Exit fullscreen mode

I `SELECT`所有(`*` =通配符)行`FROM``person`-表。
因为我们还没有添加任何数据，所以没有什么可显示的(= `0 rows`)。

* * *

## 写:将数据添加到`person`-表中:

*   检查存在哪些列以及它们需要哪些数据类型:

```
miku86-db=# \d person
                             Table "public.person"
  Column  |  Type   | Collation | Nullable |              Default               
---------------+---------+-----------+----------+------------------------------------
 id       | integer |           | not null | nextval('person_id_seq'::regclass)
 nickname | text    |           | not null | 
Indexes:
    "person_pkey" PRIMARY KEY, btree (id) 
```

Enter fullscreen mode Exit fullscreen mode

列`id`需要一个`integer`，列`nickname`需要一个`text`。
`person`表的`primary key`是`id`，因此它必须是唯一的并且不为空。

读取关于所有数据类型的[文档。](https://www.postgresql.org/docs/current/datatype.html)

*   向表格中添加新条目(=行):

```
miku86-db=# INSERT INTO person (id, nickname) VALUES (1, 'miku86');
INSERT 0 1 
```

Enter fullscreen mode Exit fullscreen mode

`INSERT``INTO``person`-表至列`id`值`1`和至列`nickname`值`miku86`。

## 读取:再次显示`person`-表格中的所有数据；

```
miku86-db=# SELECT * FROM person;
 id | nickname 
---------+----------
  1 | miku86
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

I `SELECT`所有(`*` =通配符)行`FROM``person`-表。
里面有`1 row`。

## 下一部分

我们将从我们的桌子上做更多的阅读。

* * *

## 进一步阅读

[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[PostgreSQL 数据类型](https://www.postgresql.org/docs/current/datatype.html)

* * *

## 提问

*   当您使用现有的`id`插入新行时会发生什么？
*   你总是需要写下所有的列吗？