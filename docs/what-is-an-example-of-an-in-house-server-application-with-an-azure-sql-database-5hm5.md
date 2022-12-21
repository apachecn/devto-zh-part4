# 使用 Azure SQL 数据库的内部服务器应用程序的示例是什么？

> 原文：<https://dev.to/elmomagalona/what-is-an-example-of-an-in-house-server-application-with-an-azure-sql-database-5hm5>

好吧，既然 Azure SQL 数据库运行在 Azure 上，在云中，你没有“内部”它。根据定义，它不是本地的。因此，任何应用程序的例子都将是在 Azure 内运行和/或连接到 Azure(你可以桥接到你自己的内部广告网络)并从那里消费数据的应用程序。至于应用程序的具体例子…几乎任何可以连接的应用程序都可以使用数据，所以没有指向特定应用程序的真正限制，所以这个例子是“所有应用程序”。然而，当将数据移出 Azure 时，你确实要付出更多，所以架构你的应用程序，使数据留在 Azure 内，你只需通过托管在 [Azure](https://mindmajix.com/microsoft-azure-training) 上的网页显示它，这被认为是总体上更好的方法。

将数据库即服务的概念与带有托管数据库的实例的服务器的旧概念分开非常重要。你只是以不同的方式思考它们，并以不同的方式设计解决方案。