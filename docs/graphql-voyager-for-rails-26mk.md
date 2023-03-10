# Rails 的 GraphQL 旅行者

> 原文：<https://dev.to/sourdoughdev/graphql-voyager-for-rails-26mk>

<figure>[![Exploring your GraphQL Schema with GraphQL Voyager for Rails](img/b4d4485a2acb44f19f8156a09b62ffa6.png)](///asseimg/graphql-voyager-rails/screenshot.png) 

<figcaption>使用 [GraphQL Voyager Rails](https://github.com/tamcgoey/graphql-voyager-rails) 探索您的 GraphQL 模式。</figcaption>

</figure>

用 GraphQL 支持的 API 工作和构建已经快一年了。GraphQL 最大的优点之一就是它的开发工具。

启动 [GraphiQL](https://github.com/rmosolgo/graphiql-rails) ，并在运行中创建一个查询是非常令人惊奇的。

但是，我发现在构建一个相对较新且较大的 API 时，一个难题是处理所有不同的 API 请求。

GraphiQL 对于勾画快速查询非常有用，但是当您需要启动一两周前正在处理的那个查询时，它就开始崩溃了。

这就是[失眠](https://insomnia.rest/)的由来！失眠是一个非常漂亮的 API (REST + GraphQL)客户端，它就在你的桌面上。

这对于保持您收集的所有部分构建的 API 请求非常有用。

最大的问题之一或者说真正缺乏功能是[不支持 GraphQL API 文档](https://github.com/getinsomnia/insomnia/issues/451)。

这就是 GraphQL Voyager 的用武之地。

一个很棒的 GraphQL 模式+文档浏览工具，可以很容易地深入研究您的 API。

像许多 GraphQL 工具一样，它最初是在 Rails 生态系统之外构建的。

因此，在过去的这个周末，我致力于移植构建一个 Rails 引擎 gem，让您可以轻松地在 Rails 应用程序上安装 GraphQL Voyager。

在 GraphiQL Rails 的基础上，我吸取了同样的经验，支持在任何 Rails 应用程序上轻松安装 GraphQL Voyager。

## 如何入门

首先继续添加宝石。由于这仅在开发中使用，我实际上特意对这种依赖进行了分组。

```
gem 'graphql-voyager-rails' 
```

接下来在 routes 文件中安装 rails 引擎(`config/routes.rb` )

```
if Rails.env.development?
  mount Graphql::Voyager::Rails::Engine, at: "/graphql-voyager", graphql_path: "/graphql"
end 
```

*你会注意到`Graphql`不是`GraphQL`，我希望将来能支持这种命名约定(只是想避免额外的`ActiveSupport`变化)。*

* * *

*最初发布在我的博客上:[sour dough . dev](https://sourdough.dev/graphql-voyager-rails/)T3。*