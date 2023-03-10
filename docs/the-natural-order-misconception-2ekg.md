# “自然秩序”的误解

> 原文：<https://dev.to/peledzohar/the-natural-order-misconception-2ekg>

完整版首次发布在[我的博客](https://zoharpeled.wordpress.com/2019/09/08/the-natural-order-misconception/)上。

一个常见的误解是，关系数据库表中的行有一些固有的顺序。这不是真的。

如此多的开发人员成为这种误解的受害者，尽管它在多个专业博客和论坛上被反复驳斥，我认为在多个地方写一写也无妨。

我想把这一点说清楚:

# 关系数据库表本质上是不排序的。

在 E.F. Codd 公布的关系模型中，关系被定义为一组未排序的元组。
在关系数据库中，关系被实现为表，元组被实现为行，虽然行确实以特定的顺序存储在数据库中，

**没有关系数据库可以保证没有`order by`子句的`select`语句返回的行的顺序。**

即使您指定了一个`order by`子句，如果在`order by`子句中列出的列的值不是唯一的，那么这些重复值所在行的顺序也是任意的。

一个简单的例子(T-SQL):

```
 CREATE TABLE Demo
(
    Id int NOT NULL,
    Val char(1) NOT NULL
);

INSERT INTO Demo(Id, Val) VALUES 
(1, 'A'), 
(2, 'A'),
(3, 'B'),
(4, 'C');

-- 1: -- No order by clause at all
SELECT Id, Val
FROM Demo;

-- 2: -- Order by a constant
SELECT Id, Val
FROM Demo
ORDER BY @@SPID
 -- In SQL Server, @@SPID Returns the session ID of the current user process. 
 -- This order the rows using a constant value.

-- 3: -- Order by a non-unique value
SELECT Id, Val
FROM Demo
ORDER BY Val; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例 1 和示例 2 中，(no order by 和 order by a constant)是完全等价的——数据库可以自由地以任何顺序返回行。

在示例 3 中，在`order by`子句中有一列有重复值(至少对于某些行是如此)——所以包含重复值的行是任意排序的——这个查询有两个合法的结果:

两者

```
Id  Val
1   A
2   A
3   B
4   C 
```

Enter fullscreen mode Exit fullscreen mode

和

```
Id  Val
2   A
1   A
3   B
4   C 
```

Enter fullscreen mode Exit fullscreen mode

可以由数据库返回。

请注意，这条规则也适用于窗口函数，如`row_number()`和`rank`。