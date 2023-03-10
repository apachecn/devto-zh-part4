# PostgreSQL:如何连接两个表(联接)

> 原文：<https://dev.to/miku86/postgresql-how-to-connect-two-tables-joins-2ef1>

## [T1】简介](#intro)

我们了解到:

*   [如何创建表格](https://dev.to/miku86/postgresql-how-to-create-our-first-table-1j82)
*   [如何创建简单的查询](https://dev.to/miku86/postgresql-how-to-create-some-basic-queries-57l8)

现在我们想学习如何连接两个表。

* * *

## 连接数据库外壳

首先，我连接到数据库的 shell:

```
psql -d miku86-db 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 创建两张表

### 表 1: `breed`

*   创建表格:

```
miku86-db=# CREATE TABLE breed (breed_id SERIAL PRIMARY KEY, breed_name TEXT NOT NULL);
CREATE TABLE 
```

Enter fullscreen mode Exit fullscreen mode

每个`breed`都必须有一个`breed_id`(唯一的)和一个`breed_name`。

*   将数据添加到表中:

```
miku86-db=# INSERT INTO breed (breed_name) VALUES ('Labrador');
INSERT 0 1
miku86-db=# INSERT INTO breed (breed_name) VALUES ('Poodle');
INSERT 0 1 
```

Enter fullscreen mode Exit fullscreen mode

*   检查表:

```
miku86-db=# SELECT * FROM breed;
 breed_id | breed_name 
---------------+------------
        1 | Labrador
        2 | Poodle
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

我们创造了两个品种，都有一个`breed_id`和一个`breed_name`。

### 表二:`dog`

*   创建表格:

```
miku86-db=# CREATE TABLE dog (dog_id SERIAL PRIMARY KEY, dog_name TEXT NOT NULL, breed_id INT REFERENCES breed(breed_id));
CREATE TABLE 
```

Enter fullscreen mode Exit fullscreen mode

每个`dog`必须有一个`dog_id`(唯一)和一个`dog_name`，并且可以有一个`breed_id`。

*   将数据添加到表中:

```
miku86-db=# INSERT INTO dog (dog_name, breed_id) VALUES ('Rex', 1);
INSERT 0 1
miku86-db=# INSERT INTO dog (dog_name) VALUES ('Anny');
INSERT 0 1 
```

Enter fullscreen mode Exit fullscreen mode

*   检查表:

```
miku86-db=# SELECT * FROM dog;
 dog_id | dog_name | breed_id 
-------------+----------+----------
      1 | Rex      |        1
      2 | Anny     |         
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

我们创造了两只狗，都有一只`dog_id`和一只`dog_name`、
，但只有`Rex`有一只`breed_id`、`Anny`没有(也许我们没搞清楚)。

* * *

## 连接表格

### 左表中与右表中一行匹配的每一行:内部连接

```
miku86-db=# SELECT * FROM dog JOIN breed ON dog.breed_id = breed.breed_id;
 dog_id | dog_name | breed_id | breed_id | breed_name 
-------------+----------+----------+----------+------------
      1 | Rex      |        1 |        1 | Labrador
(1 row) 
```

Enter fullscreen mode Exit fullscreen mode

`Rex`有一个`breed_id`，存在于`breed`表中，
因此他被*显示为*。
`Anny`没有，因此她是*没有显示*。

### 左表的每一行:左连接

```
miku86-db=# SELECT * FROM dog LEFT JOIN breed ON dog.breed_id = breed.breed_id;
 dog_id | dog_name | breed_id | breed_id | breed_name 
-------------+----------+----------+----------+------------
      1 | Rex      |        1 |        1 | Labrador
      2 | Anny     |          |          | 
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

`Rex`在左桌，所以他是*显示的*。
`Anny`在左表，所以她是*显示的*。

### 右表的每一行:右连接

```
miku86-db=# SELECT * FROM dog RIGHT JOIN breed ON dog.breed_id = breed.breed_id;
 dog_id | dog_name | breed_id | breed_id | breed_name 
-------------+----------+----------+----------+------------
      1 | Rex      |        1 |        1 | Labrador
        |          |          |        2 | Poodle
(2 rows) 
```

Enter fullscreen mode Exit fullscreen mode

`Labrador`在右表中，因此显示的是*。
`Poodle`在右表，所以显示的是*。**

 **### 左表各行&右表各行:全联接

```
miku86-db=# SELECT * FROM dog FULL JOIN breed ON dog.breed_id = breed.breed_id;
 dog_id | dog_name | breed_id | breed_id | breed_name 
-------------+----------+----------+----------+------------
      1 | Rex      |        1 |        1 | Labrador
      2 | Anny     |          |          | 
        |          |          |        2 | Poodle
(3 rows) 
```

Enter fullscreen mode Exit fullscreen mode

### 图形可视化

[这里的](http://www.postgresqltutorial.com/postgresql-joins/)是各种连接的可视化图形。

* * *

## 进一步阅读

[PostgreSQL 主页](https://www.postgresql.org/)
[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)
[Joins 文档](https://www.postgresql.org/docs/current/tutorial-join.html)
[图形可视化](http://www.postgresqltutorial.com/postgresql-joins/)**