# 授权系列-第 3 部分:使用 GraphQL 和规则的动态授权

> 原文：<https://dev.to/auth0/authorization-series-pt-3-dynamic-authorization-with-graphql-and-rules-3h7c>

TL；DR:在本文中，我们将演示如何使用 Flask 来构建一个 GraphQL API，它可以创建并返回灵活且有意义的数据。首先，我们将使用 Flask 和 SQLite 数据库设置我们的后端。接下来我们将引入 GraphQL 来构建一个 API，它将允许我们与魁地奇球队、球员和游戏进行交互。最后，我们将通过测试两种不同的授权模型，将权限从 Auth0 仪表板扩展到玩家，来展示这种基于图形的数据库建模可以为我们的应用程序带来的价值。

这个应用程序的所有代码都可以在 GitHub 上找到。如果您想直接进入，只需克隆存储库并按照自述文件中的说明运行应用程序。

[继续阅读📖](https://auth0.com/blog/authorization-series-pt-3-dynamic-authorization-with-graphql-and-rules/?utm_source=twitter&utm_medium=sc&utm_campaign=authorization_pt3)