# GraphQL 简介

> 原文：<https://dev.to/ashraful/introduction-to-graphql-3pc0>

## 什么是 GraphQL？

**GraphQL** 是 API 的查询语言。它满足了客户对运行时间的需求。它赋予客户端决定 API 数据格式、数据集的权力。

## 令人敬畏的 GraphQL 背后的历史

令人敬畏的 GraphQL 是由**脸书**在 2012 年发明的。当时脸书内部使用它。后来，2015 脸书决定让它开源。现在由 GraphQL 社区维护。

## 道具

#### *单端点 API 服务

GraphQL 能够在单个端点上提供大量数据。例如，您需要系统的所有用户和所有产品的列表。在 REST 中，至少需要两个端点来实现这一点。但是在 GraphQL 中，它只存在于一个 API 中。
**一个基本的 GraphQL 查询示例:**

```
query  getCustomData  {  getUsers  {  name  username  }  getProducts  {  name  }  } 
```

Enter fullscreen mode Exit fullscreen mode

#### *控制数据抓取

从上一个例子中你已经了解了数据查询。在 GraphQL 中，我们可以让服务器知道我真正需要的是什么。
**让我们修改查询**

```
query  getCustomData  {  getUsers  {  name  username  email  ...  anything  you  have  }  } 
```

Enter fullscreen mode Exit fullscreen mode

#### *现代设计

GraphQL 具有非常现代的 API 制作设计。它有助于开发更容易和更少的维护不同的不同客户端软件。例如，您有一个桌面客户端、一个移动客户端和一个 web 客户端，显然是针对产品名称“x”的。移动应用不需要你的网络客户端和桌面客户端需要的所有数据。在这种情况下，要么您需要为每个客户端公开不同的 API，要么序列化所有字段。然后，你的一个客户端将获取额外的/不必要的数据。现在想想非常慢的网络。希望你能理解装载缓慢的痛苦。因此，主要且明智的解决方案是编写 GraphQL API。

### 弊

#### *无内置缓存支持

不幸或幸运的是，GraphQL 不支持缓存。因为 REST api 有多个端点。所以，传统的 http 调用缓存工作得很好。与 REST 不同，它只有一个端点来获取数据。数据依赖于查询。所以，没有办法缓存来自 http 的请求。

#### *没有具体的错误代码

这不是一个大问题。我们可以秘密解决这个问题。与 REST 不同，它没有不同的错误代码。它总是返回 200。识别错误的方法是响应数据中的关键`errors`。

**结论:**个人观点 GraphQL 很牛逼，很聪明，很现代。但是除非需要，否则不要用。