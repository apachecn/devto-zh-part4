# 使用 GraphQL 连接 Google Places API 和 Postgres 中的数据，构建基于位置的应用程序

> 原文：<https://dev.to/hasurahq/using-graphql-to-join-data-across-google-places-api-and-postgres-to-build-location-based-apps-4knl>

## TL；速度三角形定位法(dead reckoning)

使用 Hasura Remote Joins 将 Postgres 数据库中的地理数据与 GraphQL 中的 Google Places API 连接起来。样板文件-> [Google Places](https://github.com/praveenweb/google-places-remote-schema)

<figure>[![](img/1c2ae120e516a3c9a1e823e7ce7496ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vF9XbU_O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/07/google-places-remote-schema-banner.png) 

<figcaption>Hasura 远程加入 Google Places API 和 PostGIS</figcaption>

</figure>

这篇文章是我们的[远程连接](https://blog.hasura.io/remote-joins-a-graphql-api-to-join-database-and-other-data-sources/)(在预览版中可用)系列的一部分。Hasura 中的远程连接允许您跨表和远程数据源连接数据。如果你想在我们发布时得到通知，你可以在这里注册[。前往](https://hasura.io/remote-joins)[公关](https://github.com/hasura/graphql-engine/pull/2395)查看更多细节，阅读预览文件，并尝试一个你可以旋转的测试 Hasura 图像。跳到我们的[不和谐](https://discord.gg/hasura)或者评论 [github](https://github.com/hasura/graphql-engine) ，让我们知道你的想法！

在这个例子中，我们将看看如何使用 Hasura 将 Google Places API 中的数据与 Postgres 中的现有地理数据结合起来。

## **波斯特吉斯**

为了存储和计算地理位置数据，我们将使用`PostGIS`，它是`Postgres`的空间数据库扩展器。它在 Postgres 上增加了对地理对象的支持，还提供了一些实用功能，如距离、面积、并集和交集。

### **启用后缀**

在开始之前，我们需要在 Postgres 上启用 PostGIS 扩展。

前往`API Console`中的`Data`选项卡，点击左侧导航栏中的`SQL`。并执行以下 SQL 命令: