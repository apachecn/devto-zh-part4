# SQL 视图中的动态模式名称

> 原文：<https://dev.to/peledzohar/dynamic-schema-name-in-sql-view-38b5>

我最近在 Stackoverflow 上回答了一个 T-SQL 问题。不幸的是，我在它发表后很久才回答，所以我甚至不确定 OP 是否看到了我的回答。

这个问题的点击率很低(目前只有 45 次)，我想知道解决这个问题的其他方法。我很确定我的建议会解决这个问题，但是由于它使用 DDL 触发器(我没有这方面的实际经验)，我想知道是否有更好的方法。你觉得怎么样？有什么建议吗？

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png)[SQL 视图中的动态模式名](https://stackoverflow.com/questions/56462131/dynamic-schema-name-in-sql-view)

Jun 5 '19 Comments: 6 Answers: 1[![](img/e3f0373ec76330150a340eacd410b600.png)1![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/56462131/dynamic-schema-name-in-sql-view) </header>

我有两个数据集:

1.  一个是关于狗的数据[我的数据]
2.  第二个是匹配键的查找表[我不能控制这个数据]

匹配的密钥定期更新，我想创建一个狗的视图(或满足相同目的的东西)

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/56462131/dynamic-schema-name-in-sql-view)</button>