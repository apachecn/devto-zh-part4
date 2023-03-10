# 什么是 GraphQL(第 1 部分)？

> 原文：<https://dev.to/quickly_react/what-is-graphql-part-1-3me4>

[![intersections](img/d48cd8141907b3c74ebb72f6ce0f0dad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QD07WRf2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1465447142348-e9952c393450%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D968%26q%3D80)

在我们上一篇[帖子](https://dev.to/what-is-gatsbyjs/)中，我们探讨了 GatsbyJS，但我们忽略了 GatsbyJS 是如何使用 GraphQL 的。

我们认为，GraphQL 值得拥有自己的博客——也就是说，更多的内容！🤟。

## 什么是 GraphQL？

[![here we go](img/ec13e4cd34b74e8351a431aef0f38d33.png)](https://i.giphy.com/media/xThtaaVMcDRLZXEy9W/source.gif)

GraphQL 是一种 API 查询语言，也是一种用现有数据完成这些查询的运行时语言。

GraphQL 有一个很好的短语来解释这一点:

> 要求你所需要的，得到它

每个人:嗯，不太清楚 GraphQL 是做什么的，用在哪里？

有道理😓。

假设你想建一个博客。你需要两页纸:

*   列出所有文章的页面。

*   显示个人文章及其内容的页面。

我们的 API 输出结构如下所示:

1.  为了得到一个职位。

```
{
    id: string,
    title: string,
    body: string
} 
```

1.  列出所有的职位

```
{
    posts: [Post] // a array of all the posts
} 
```

现在事情变得有点有趣了。

现在，如果您看到清单`Posts`，我们的查询结构发送关于每个`Post`的所有信息，包括我们不想列出的每个`Post`的主体，这只是增加了有效负载。

*GraphQL 已进入聊天状态*

有了 GraphQL，这就容易多了。您可以指定适当的查询结构来获得您想要的结果。现在，帖子应该是这样的:

```
{
    posts: [
      {
        id: string,
        title: string
      }
      ...
    ]
} 
```

*大家:GraphQL 从何而来，力挽狂澜？*

[![suspicious](img/aac5a554c571abfebb2de598cd4f4a44.png)](https://i.giphy.com/media/4YWtPFasSBZMFrs7ML/giphy.gif)

现在，到目前为止，还不清楚 GraphQL 是如何解决这个问题的。但是，GraphQL 的功能现在一定变得更加清晰了。

## graph QL 如何化险为夷？

GraphQL 以两种方式拯救世界:

1.  **服务器端**:可以从服务器端使用 GraphQL。

注意:有各种各样实现，但是我们的核心是 javascript。我们选择使用[express](https://expressjs.com/)(node . js 的 web 框架)

所以，假设你有一个 web 应用程序，想从后端集成 GraphQL，你可以使用 [express-graphql](https://github.com/graphql/express-graphql) 。

从[文档](https://graphql.org/graphql-js/running-an-express-graphql-server/)中选择，一个简单的设置是:

```
var express = require('express');
var graphqlHTTP = require('express-graphql');
var { buildSchema } = require('graphql');

// Construct a schema, using GraphQL schema language
var schema = buildSchema(`
  type Query {
    hello: String
  }
`);

// The root provides a resolver function for each API endpoint
var root = {
  hello: () => {
    return 'Hello world!';
  },
};

var app = express();
app.use('/graphql', graphqlHTTP({
  schema: schema,
  rootValue: root,
  graphiql: true,
}));
app.listen(4000); 
```

如果您在本地运行，您可以检查`http://localhost:4000/graphql`

[![graphql](img/9e5244b5c0a2526db4f6ba7f9c663066.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RykWhJpR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://graphql.org/img/hello.png)

每个人:那么，这是不是意味着我们不再需要休息了？

很多部分仍然缺失，但是剧透:**是的**你可以从 REST 切换到 GraphQL。虽然，目前大多数的实现更多的是围绕 REST 的 GraphQL 的包装。

这让我们想到...

1.  **API 包装器**

与其他 eg 方法相比，GraphQL 相对较新(于 2015 年开源);REST(1999 年发布)。目前业界已经开始接受的方法是在现有方法上使用 GraphQL 的 API 包装器，所以不管是 REST 还是 SOAP 都没关系。

我们将在第 2 部分(正在进行中)对此进行探讨，我们还将讨论如何与 React.js 集成。

[![all about](img/8e6ffc7025b82098fa246a6ea695197d.png)](https://i.giphy.com/media/fxQZDPoO58TyBhhkwg/source.gif)

## 为什么我们要将 GraphQL 作为新标准？

GraphQL 赋予的核心功能之一是前端可以请求它想要的确切数据。

*大家:这有什么关系，RESTful API 的扁平化结构还不够吗？*

这可能是我们在真正了解 GraphQL 的起源之前无法真正理解的问题之一。

我们想过写下来，但是有一部非常好的纪录片😍我们想留给你的。他们追溯了 GraphQL 的整个起源，以及脸书、Github 和 Twitter 等公司是如何做到的。，正在采用它。

这篇博客的总体目标是讨论 GraphQL 是如何工作的，并简要概述一下神奇是如何发生的。

*敬请关注第二部分*

你也可以关注我们在[https://blog.quicklyreact.com](https://blog.quicklyreact.com)的博客

[![bye](img/a221a20c97cf20a5df4293ed44e26cfd.png)](https://i.giphy.com/media/k4ta29T68xlfi/giphy.gif)