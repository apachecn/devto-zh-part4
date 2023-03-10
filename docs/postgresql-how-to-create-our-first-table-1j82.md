# PostgreSQL:如何创建我们的第一个表

> 原文：<https://dev.to/miku86/postgresql-how-to-create-our-first-table-1j82>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了](https://dev.to/miku86/postgresql-what-is-it-how-to-install-it-16hk)和 [setup](https://dev.to/miku86/postgresql-how-to-setup-our-installed-postgresql-39hf) PostgreSQL。

现在我们想学习如何创建我们的第一个表。

* * *

## 连接数据库外壳

我们刚刚在[设置文章](https://dev.to/miku86/postgresql-how-to-setup-our-installed-postgresql-39hf)中创建了一个用户和一个数据库。

现在我们可以连接到数据库外壳:

```
psql -d [db] 
```

Enter fullscreen mode Exit fullscreen mode

我的结果，因为我之前创建了一个数据库“miku 86-db”:

```
miku86-db=# 
```

Enter fullscreen mode Exit fullscreen mode

[psql](https://www.postgresql.org/docs/current/app-psql.html) 是 PostgreSQL 的一个基于终端的前端，可以输入查询，发布给 PostgreSQL，查看查询结果。

* * *

## 基本命令

*   psql 帮助:`help`
*   psql 命令:`\?`
*   SQL 命令:`\h`
*   退出 psql: `\q`

*   当前连接信息:`\conninfo`

*   数据库列表:`\l`

*   连接到数据库:`\c [dbname]`

*   创建数据库:`CREATE DATABASE [db];`

*   删除数据库:`DROP DATABASE [db];`

*   显示数据库的表格:`\dt`

*   显示表格概述:`\d [table]`

*   创建表格:`CREATE TABLE [table]([col][datatype] [constraints])`

* * *

## 创建我们的第一张表

*   检查我当前的连接:

```
miku86-db=# \conninfo
You are connected to database "miku86-db" as user "miku86" via socket in "/run/postgresql" at port "5432". 
```

Enter fullscreen mode Exit fullscreen mode

*   显示数据库的表格:

```
miku86-db=# \dt
Did not find any relations. 
```

Enter fullscreen mode Exit fullscreen mode

*   创建表格:

```
miku86-db=# CREATE TABLE person (id SERIAL PRIMARY KEY, nickname TEXT NOT NULL);
CREATE TABLE 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚在数据库中创建了一个包含两列的表:

*   列#1:一个`id`，也就是一个 [`SERIAL`](https://www.postgresql.org/docs/current/datatype-numeric.html) (=一个`integer`，总是增加`1`)和一个 [`PRIMARY KEY`](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-PRIMARY-KEYS) ，这意味着`id`是表中一行的唯一标识符，因此值是唯一的并且不为空

*   列#2:一个`nickname`，那是一个 [`TEXT`](https://www.postgresql.org/docs/current/datatype-character.html) (=可变且长度不限)，那是 [`NOT NULL`](https://www.postgresql.org/docs/current/ddl-constraints.html#id-1.5.4.5.6) ，意思是`nickname`不能少。

*   显示表格概述:

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

## 下一部分

我们将从我们的表中读写。

* * *

## 进一步阅读

[PostgreSQL 主页](https://www.postgresql.org/)
[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[PostgreSQL 数据类型](https://www.postgresql.org/docs/current/datatype.html)
[PostgreSQL 约束条件](https://www.postgresql.org/docs/current/ddl-constraints.html)

* * *

## 提问

*   你最喜欢的 SQL 数据库是什么？