# 使用 SQL 流存储的事件源

> 原文：<https://dev.to/codeopinion/event-sourcing-with-sql-stream-store-4p4o>

[![Event Sourcing with SQL Stream Store](img/e0f83abcd46ca43699167e75065c5d2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uu47zhPD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeopinion.com/wp-content/uploads/2017/11/Numismatics_and_Notaphily_icon-300x216.png)

我对 SQL 流存储有所了解(我相信当时它被称为 Cedar ),但还没有真正深入了解它。前提是在 SQL 上提供流存储。目前支持 MS SQL Server、PostgreSQL 和 MySQL。这意味着它只是一个可以与 SQL 实现一起使用的. NET 库。让我们看看如何使用 SQL 流存储实现事件源。

## SQL 流存储演示应用程序

对于这个演示/示例，我将创建一个. NET Core 3 控制台应用程序。我们的想法是创建一个银行账户的原型事件源示例。

我想探索事件流所需的主要功能。

*   创建流
*   向流中追加新事件
*   从流中读取事件
*   订阅流以接收附加事件

## NuGet 包

和往常一样，第一件事是通过将 **SqlStreamStore** [NuGet 包](https://www.nuget.org/packages/SqlStreamStore)作为 PackageReference 添加到 csproj 中来获得它。