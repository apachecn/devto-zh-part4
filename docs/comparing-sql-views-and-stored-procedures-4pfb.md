# 比较 SQL 视图和存储过程

> 原文：<https://dev.to/rachelsoderberg/comparing-sql-views-and-stored-procedures-4pfb>

我的工作场所在我们的数据管理中使用了大量的存储过程和视图，因此，当我为公司开发应用程序时，我不得不学习使用甚至构建更多的存储过程和视图。虽然我们每天都在使用它们，但存储过程和视图对我来说有点神秘，我有几个问题:

视图是做什么的？存储过程是做什么的？
什么时候应该使用视图，什么时候应该使用存储过程？
性能上有区别吗？

当我问我的经理这些问题时，我得到了一个简短的回答，但我不满意，需要深入了解。这篇文章是这种挖掘的结果，希望它能为其他具有类似经验水平的开发人员提供一些见解，这样我们就能学会不仅在我们的代码中，而且在我们如何管理我们的数据中使用最佳实践。

## 视图是做什么的？

顾名思义，视图就是一个“窗口”,您可以在其中观察数据的特定部分。当开发人员经常运行查询并希望自动化或简化他们的过程时，他们通常会创建一个视图。

### 显示视图中的数据

视图可以被认为是一个虚拟表，在查询数据时，它的工作方式与数据库中的任何其他表非常相似。使用 SELECT 语句，可以使用 WHERE 子句查询视图的全部或特定部分:

*SELECT * FROM vAccounts
其中 accountId = '523'*

您还可以在视图和表中加入:

*SELECT * FROM vAccounts vAcc
pho . account id = vAcc . account id*上的内部连接电话号码 pho

## 存储过程是做什么的？

存储过程是可以从应用程序调用(或在 SQL Management Studio 中运行)的逻辑封装。它们通常用于在外部应用程序传入参数后对数据执行常见的插入、删除和更新语句。它们通常包含各种循环、变量以及对其他存储过程、表和视图的调用。

### 显示存储过程中的数据

与视图不同，存储过程需要测试和运行 EXECUTE 语句和传入的参数值:

执行 spAccount ' James

上面返回的结果将是一个与 James 这个名字有某种关联的虚拟表(我们需要看到存储过程才能确定)。

## 什么时候应该使用视图，什么时候应该使用存储过程？

最简单地说，当只需要 SELECT 语句时，就使用视图。视图应该用来存储常用的连接查询和特定的列，以构建我们希望看到的一组精确数据的虚拟表。存储过程包含更复杂的逻辑，如 INSERT、DELETE 和 UPDATE 语句，以自动化大型 SQL 工作流。

## 性能有区别吗？

在 Grant Fritchey [所做的测试中，可怕的 DBA 存储过程并不比视图](https://www.scarydba.com/2016/11/01/stored-procedures-not-faster-views/)快，与普遍的看法相反，尽管 SQL 视图和 SQL 存储过程是“根本不同的对象”，但它们的性能是完全相同的 Fritchey 运行了几千次视图、带视图的存储过程和存储过程的执行，每次执行 8 次逻辑读取，以检索相同的数据集。他的结果显示了 10 微秒(5%)的微小差异，他认为这可能是 I/O、CPU 或其他方面的差异。

Fritchey 能找到的唯一主要区别是编译时间——存储过程的编译时间比视图快得多，这是任何寻求性能提升的人的优势所在。

来源:
[微软——第一课:创建数据库对象](https://docs.microsoft.com/en-us/sql/t-sql/lesson-1-creating-database-objects?view=sql-server-2017)
[bytes.com——“视图 vs 存储过程:有什么区别？”](https://bytes.com/topic/sql-server/answers/450173-views-vs-stored-procedures-whats-difference)
[stackoverflow.com——“存储过程和视图有什么区别？”](https://stackoverflow.com/questions/5194995/what-is-the-difference-between-a-stored-procedure-and-a-view)

* * *

如果你想在社交媒体上与我交流，来 Twitter 或 T2 LinkedIn 打个招呼吧！