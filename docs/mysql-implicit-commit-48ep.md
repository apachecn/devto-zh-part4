# MySQL 隐式提交

> 原文：<https://dev.to/mcloide/mysql-implicit-commit-48ep>

如果你不明白它是如何工作的，MySQL 隐式提交可能是让你吃惊的特性之一。我不会贸然质疑为什么要这样做，我相信这背后有一个非常合理的原因，但是，尽管如此，如果你不理解 MySQL 的这个特性，它会对你不利。

这个特性很简单:在特定的情况下，不管 MySQL 的配置如何，它都会隐式地提交前面的语句。

这意味着，如果您有一堆插入，然后有导致隐式提交的东西，那么所有这些插入，不管隐式提交语句的结果如何，都将被提交，使数据库处于一种奇怪的状态。

有时文本没有帮助，所以让我们转向一些代码或某种形式的代码。

```
START TRANSACTION
INSERT
INSERT
INSERT
INSERT
ALTER TABLE
...

```

不管 ALTER TABLE 的结果如何，基于 MySQL 的这一特性，在它之前的所有提交都将被提交，而 ALTER TABLE 之后的任何内容(如果没有失败)都将启动一个新的事务。

以下情况也会出现同样的行为:

*   任何结构变化
*   另一个事务开始(注意 MySQL 有保存点的概念，但没有嵌套事务)
*   任何 BEGIN 语句(有两种类型)

那么这对你有什么影响呢？假设您正在恢复一个 SQL 转储，一组包含以下数据的表已被正确创建和植入，但有一个表没有。那一刻之前的一切都将被提交，之后的一切都将失败。

另一种情况是数据迁移脚本或试图重建/更改数据的脚本。不理解隐式提交会使数据库处于不良状态。

关于它还有很多要讨论的，但是，请阅读一下文档。它值一百万。

ref:[https://dev . MySQL . com/doc/ref man/5.7/en/implicit-commit . html](https://dev.mysql.com/doc/refman/5.7/en/implicit-commit.html)

本文原载:[https://bit.ly/2Z5EzF1](https://bit.ly/2Z5EzF1)