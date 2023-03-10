# Postman 现在支持 GraphQL

> 原文：<https://dev.to/graphqleditor/postman-now-supports-graphql-4hnc>

[GraphQL](https://graphql.org/) 是一种 API 的查询语言。它变得越来越流行，因为与 REST APIs 相比，它提供了许多好处，其中最重要的是更智能的数据获取。

GraphQL 提供的查询模型在处理大型 API 时显示了它的真正威力。

### 图 QL vs REST

GraphQL 允许指定我们在查询中需要什么数据，以及响应中将包含什么数据——不多也不少，只是我们在单个请求中请求的数据。另一方面，当用 REST 获取相同的数据时，我们会以多次 REST 往返结束...

[![](img/fc0bf0938bcb1f93e0e9dbe0787b9eea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VvOpru-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yi7w5l3qvilfthdifkz8.png)

###### 来源:【blog.apollographql.com】T2

...或者这样想象:

[![REST vs GraphQL](img/5136fbf1cf2006beced9119e72f92dcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--27ON1SMp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0trklssn1tp4j9aj78ca.png)

###### 来源:【crystallize.com】T2

### 邮递员中的 GraphQL

Postman 加入了承认 GraphQL 质量的公司行列，最近[宣布在其服务中支持 GraphQL](https://blog.getpostman.com/2019/06/18/postman-v7-2-supports-graphql/) 。Postman v7.2 的最新版本支持:

*   在请求正文中发送 GraphQL 查询，
*   GraphQL 变量，
*   GraphQL 查询自动完成。

这是对之前引入模式支持的更新的一个很好的补充，所有这些使得在 Postman 中直接创建和存储 GraphQL 模式成为可能！

[![GraphQL BETA in Postman](img/8a3a8f9959d3373153920ade79216f16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--blRlIUO6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/incr6w2pbc4sboj1hm5g.png)

###### 来源:【blog.getpostman.com】T2