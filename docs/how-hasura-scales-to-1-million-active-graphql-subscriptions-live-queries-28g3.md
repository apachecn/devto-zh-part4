# Hasura 如何扩展到 100 万个活动 GraphQL 订阅(实时查询)

> 原文：<https://dev.to/hasurahq/how-hasura-scales-to-1-million-active-graphql-subscriptions-live-queries-28g3>

[![](img/6fa2d617d0b60c008518427ec90e271c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XzYiQ5x3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/12/1-million-subs.png)

Hasura 是 Postgres 上的一个 GraphQL 引擎，为即时 GraphQL APIs 提供授权。Hasura 允许客户端进行“实时查询”(通过 GraphQL 订阅)。例如，食品订购应用程序可以使用实时查询来显示特定用户订单的实时状态。

我们设计的 hasura 一开始就具有可扩展性。我们最近测试了 Hasura 处理一百万个活动查询的性能。作为测试设置的一部分，多个客户端(一个 web/移动应用程序)使用 auth 令牌订阅数据或实时结果。数据在 Postgres 中。Postgres 中每秒更新 100 万行(确保每个客户端推送一个新结果)。Hasura 是 GraphQL API 提供者(有授权)。

**结果:**在 100 万个实时查询(因此有 100 万个打开的 websocket 连接到 Hasura)的情况下，Postgres 处于大约 28%的负载之下，Postgres 连接的峰值数量大约为 850。

我们已经在 github repo 上写了实现这种性能的架构(以及关于我们的基准测试过程和工具的更多注释)。

点击这里查看:[https://github . com/hasura/graph QL-engine/blob/master/architecture/live-queries . MD](https://github.com/hasura/graphql-engine/blob/master/architecture/live-queries.md)