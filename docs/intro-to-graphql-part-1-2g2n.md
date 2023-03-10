# GraphQL 简介(第 1 部分)

> 原文：<https://dev.to/dimer22zhc/intro-to-graphql-part-1-2g2n>

## 什么是 GraphQL？

GraphQL 是如何与 API 对话的规范。它通常在 HTTP 上使用，其中的关键思想是向 HTTP 端点发送一个“查询”，而不是针对不同的资源访问不同的 HTTP 端点。

GraphQL 是为 web/移动应用程序(HTTP 客户端)的开发人员设计的，他们可以通过 API 调用方便地从后端 API 获取所需的数据。

## GraphQL vs REST:一个例子:

假设您有一个 API 来获取用户的个人资料和他们的地址。在典型的 REST 场景中，请求/响应应该是这样的:

[https://graph QL-engine-cdn . hasura . io/learn-hasura/assets/graph QL-react/rest-API . png](https://graphql-engine-cdn.hasura.io/learn-hasura/assets/graphql-react/rest-api.png)

*如果您的 API 服务器是 GraphQL 服务器，那么您的 API 调用应该是这样的:*

[https://graph QL-engine-cdn . hasura . io/learn-hasura/assets/graph QL-react/graph QL-API . gif](https://graphql-engine-cdn.hasura.io/learn-hasura/assets/graphql-react/graphql-api.gif)

**可以看到，对于客户端发送的不同“查询”，响应 JSON 是不同的。**

```
Request1:         |  Response1:
query {           |  {
  user (id: 1) {  |    "user": {
    id            |       "id": 1
  }               |     }
}                 |  }
----------------------------------------
Request2:         |   Response2:
query {           |   {
  user (id: 1) {  |     "user": {
    id            |       "id": 1
    name          |       "name": "Elmo"
  }               |     }
}                 |   } 
```

## 思维中的 GraphQL

我们正在改变我们看待 API 调用的方式。我们不是对不同的 URL 进行不同的 API 调用来获取数据，而是对一个“单一 URL 端点”进行特别查询，该端点根据查询返回数据。

你不是“得到”一个资源，而是“发布”一个描述你想要什么数据的查询。

你认为你的 API 返回的数据是一个“图形”,这允许你通过查询一次提取“相关的”数据。在上面的例子中，您在同一个 API 调用中获取用户和用户地址(作为嵌套的 JSON 对象),而不是进行两次 API 调用。

您在 POST 请求中作为数据发送的“查询”具有结构和语法。这种“语言”被称为 GraphQL。

正如你在上面的例子中所看到的，GraphQL 查询看起来非常简洁易读！这是因为查询是您想要的最终 JSON 数据的“形状”。这是让 GraphQL 充满乐趣的关键原因之一！

## GraphQL 的好处

***避免过量提取*** :您可以避免提取超出您需要的数据，因为您可以指定您需要的确切字段。

***防止多次 API 调用*** :万一需要更多数据，也可以避免多次调用自己的 API。在上面的例子中，您不需要进行两次 API 调用来分别获取用户和地址。

***减少与 API 开发者的沟通*** :有时候为了获取你所需要的确切数据，特别是如果你需要获取更多的数据并且想要避免多次 API 调用，你将需要要求你的 API 开发者构建一个新的 API。有了 GraphQL，你的工作就独立于 API 团队了！这可以让你在应用上工作得更快。

***自文档化:*** 每个 GraphQL API 都符合一个“模式”，即图形数据模型和客户端可以进行的查询类型。这使得社区可以构建很多很酷的工具来探索&,可视化你的 API 或者创建 IDE 插件来自动完成你的 GraphQL 查询，甚至是“代码生成”。稍后我们将更详细地了解这一点！

*这里有一个快速图表向您展示典型 REST 式术语的 GraphQL 类似物*:

* * Requirement | * * REST | * * graph QL |
获取数据对象| GET| query|
写入数据| POST| mutation|
更新/删除数据| PUT/PATCH/DELETE| mutation|
观看/订阅数据| - |订阅|数据