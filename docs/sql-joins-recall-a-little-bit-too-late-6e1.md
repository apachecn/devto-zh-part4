# SQL 连接召回——有点太晚了

> 原文：<https://dev.to/rbglod/sql-joins-recall-a-little-bit-too-late-6e1>

不久前，我参加了一家非常酷的公司的面试。我完全搞砸了，尽管事实上...问题真的很基本。有两个任务:一个在 Ruby 中，一个在 SQL 中。Ruby 花了我不到 10 分钟，但是 SQL...说起来很尴尬。

问题是关于在一个查询中连接三个表...使用 ActiveRecord 时，我是在运行中完成的，但后来我意识到我是一个没有 SQL 技能的 Rails 开发人员。可耻。所以让我们从简单的连接开始。

## 归附

[![img](img/938a5c35a949848c6c1075dc67354c8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iF5-P3Ug--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Hs9auHg.jpg)

假设我们有两张桌子:

*   书籍，
*   作者，

具有以下结构:

```
books |   
------|   
id    |   
title |
author_id|

authors|
-------|
id     |
name   | 
```

以及以下数据:

```
test=# select * from books;
 id |       title       | author_id 
----+-------------------+-----------
  1 | Sapiens           |         3
  2 | Homo Deus         |         3
  3 | Harry Potter      |         2
(3 rows)

test=# SELECT * FROM authors;
 id |     name     
----+--------------
  1 | Mr. Hopkins
  2 | J.K. Rowling
  3 | Yuval Noah Harari
(3 rows) 
```

你看到了它们之间的联系，对吗？作者有许多书，书属于作者。

### 内部连接

所以现在，我们想要的是只选择那些被分配了书籍的作者。

```
SELECT authors.* FROM authors
  INNER JOIN books ON books.author_id = authors.id; 
```

我们在这里做的是`INNER JOIN`——我们连接两个表并打印出记录，它们有匹配的值(`authors.id`等于`books.author_id`)。
如果我们不想重复打印出来的作者，我们应该在`SELECT`后面加上`DISTINCT`关键字。

### 全外连接/全连接

要从两个表中选择所有数据，我们应该使用`FULL OUTER JOIN` :

```
SELECT * FROM authors
  FULL OUTER JOIN books ON books.author_id = authors.id; 
```

它打印出所有数据 books 表中的所有记录和 authors 表中的所有记录。与`FULL JOIN`相同

### 左连接/左外连接

Left join 返回来自`authors`(左边的表)的所有记录——如果我们从`books`开始，当然是`books`——以及来自 joined 表- `books`的匹配记录。
那么，让我们打印所有的作者，以及他们的书的数量！

```
SELECT authors.name, COUNT(authors.name) AS books_count FROM authors
  LEFT OUTER JOIN books
  ON authors.id = books.author_id
  GROUP BY authors.name; 
```

它打印出漂亮的分组数据:

```
 name          | books_count 
-------------------+-------------
 Yuval Noah Harari |     2
 J.K. Rowling      |     1
 Hopkins           |     1
(3 rows) 
```

### 超过两桌怎么办？

在某些情况下(例如在招聘面试中)，您需要连接两个以上的表并从中获取数据。

我们在前面的基础上再加一张表。

```
authors_books
-------------
author_id
book_id 
```

并移除柱`books.author_id`。它让我们产生多对多的联想。

现在，这个表中的记录应该是这样的:

```
test=# SELECT * FROM authors_books;
 id | author_id | book_id 
----+-----------+---------
  1 |         3 |       1
  2 |         3 |       2
  3 |         2 |       3
(3 rows) 
```

要打印对应书籍的作者，我们需要将`authors`和`authors_books`连接起来，然后将`authors_books`和`books`连接起来，以匹配给定的 id。
让我们试试这个 SQL 查询:

```
SELECT authors.name, books.title FROM authors 
  JOIN authors_books ON authors.id = authors_books.author_id
    JOIN books ON authors_books.book_id = books.id; 
```

```
 name        |       title       
--------------------+-------------------
 Yuval Noah Harari  | Sapiens
 Yuval Noah Harari  | Homo Deus
 J.K. Rowling       | Harry Potter
(3 rows) 
```

现在，我们也可以数一数他们的书了:

```
test=# SELECT authors.name, COUNT(authors.name) FROM authors
         JOIN authors_books ON authors.id = authors_books.author_id
           JOIN books ON authors_books.book_id = books.id GROUP BY authors.name;

        name        | count 
--------------------+-------
 Yuval Noah Harari  |     2
 J.K. Rowling       |     1
(2 rows) 
```

### 现在足够加入了

正如您所看到的，SQL 连接查询并不像看起来那样困难和可怕。ActiveRecord 使得 Rails 开发人员变得懒惰，并且禁用了 sql(就像我一样)，但是有时候知道如何在原始 SQL 中编写一些复杂的连接是值得的。