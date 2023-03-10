# 关于 GraphQL 和 REST / JSONiq 的思考

> 原文：<https://dev.to/johanneslichtenberger/thoughts-on-graphql-and-rest-jsoniq-40he>

大家好，

只是一个快速的想法...假设您提供了一个通用端点并拥有类似 JSONiq[1]的查询功能，这基本上是基于 JSON 的 NoSQL 文档库的 XQuery。然后可能还有一个 Websocket 来“订阅”服务器上的更改。

拥有类似 SQL 的东西而不是树形结构(在这个例子中是 JSON)数据不是更强大吗？至少我认为它比 GraphQL 提供的要强大得多，但是它可能缺少工具支持。

也就是说，您可以用一个更通用的 REST API 优雅地组合或总结这种方法。我认为您只需要知道如何像在 GraphQL 中一样将对象序列化为 JSON。

致以亲切的问候，并祝周末愉快:-)
约翰内斯

[1][http://jsoniq.org/](http://jsoniq.org/)