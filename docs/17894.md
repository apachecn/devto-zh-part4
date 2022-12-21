# 使用 Macrometa 的地理分布式数据库云构建低延迟的全球分布式 Python 应用程序

> 原文：<https://dev.to/chetan_/using-python-to-create-a-globally-replicated-database-on-macrometa-5ebm>

在本教程中，我将向您展示如何快速、轻松地构建一个跨越北美(洛杉矶和阿什伯恩，弗吉尼亚州)、欧洲(法兰克福)和亚洲(印度孟买)的分布式数据库后端。

我们将在 Macrometa 的无服务器地理分布式数据库云([https://www.macrometa.co](https://www.macrometa.co))上构建简单的 python 应用程序，以创建跨越多个全球数据中心的数据服务后端。

这是为了当最终用户运行你的应用程序时，它不需要从数千英里和数百毫秒之外的数据库中获取数据，而是从离它最近的区域中最近的运行数据库中获取数据。

如果你想知道什么是宏梅塔？Macrometa 是一种地理分布式数据库云服务，开发者可以免费在其上构建应用。Macrometa 提供以下功能:

1.  一个无服务器的实时 noSQL 地理分布式数据库，以键/值对、JSON 文档、流、图形和时间序列的形式提供数据。地理分布位在这里很重要，因为这正是 Macrometa 的特殊之处——它可以非常高效、快速和准确地在全球数百个位置复制和同步您的数据，同时隐藏提供正确并发行为的复杂性。

2.  一个完全集成的流引擎，允许您查询和操作 motion 中的数据，而不需要等待数据被接收到数据库中(我将在几天后就此做一个单独的教程)

3.  一个无服务器计算运行时，它在 V8 引擎中提供了一个 Web 程序集，让您可以运行与数据高度相关的代码(这将在 2019 年下半年推出，目前的产品中没有)

在 https://www.macrometa.co 了解更多信息

有了这个背景，让我们开始吧。

完成本教程有两种方法:

1.  对于那些想编码的人，下面的 repl.it 是给你的。您可以看到如何构建一个简单的应用程序，用 Python 调用 Macrometa 的 API 来做有趣的事情。
2.  对于那些喜欢网络控制台或图形用户界面的人来说，去 https://www.macrometa.co/quickstart

构建应用程序包括 3 个基本步骤:

1.  创建地理结构或使用全局结构
2.  为您的数据创建一个集合(集合就像 SQL 中的一个表)
3.  发送查询来创建、更新、删除(CRUD)集合中的文档(行)

[https://repl.it/@ChetanVenkatesh/Programming-Macrometa-Global-geo-distributed-DB-in-Python?lite=true](https://repl.it/@ChetanVenkatesh/Programming-Macrometa-Global-geo-distributed-DB-in-Python?lite=true)