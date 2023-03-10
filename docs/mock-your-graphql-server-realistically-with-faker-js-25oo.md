# 用 faker.js 真实地模拟您的 GraphQL 服务器

> 原文：<https://dev.to/yvonnickfrin/mock-your-graphql-server-realistically-with-faker-js-25oo>

有时，您的 GraphQL 服务器需要使用一个尚未实现或暂时不可用的 API。在这种情况下，模仿似乎是正确的事情，但很难保持良好的模仿数据，所以我们最终在应用程序中到处都是“Lorem ipsum”。

## faker.js

faker.js 是一个可以让你在节点或浏览器中生成虚假数据的库。它采用了许多方法来为常见用例生成数据，例如:

*   名称
*   地址
*   电话
*   形象
*   公司
*   ...

即使对于...Lorem ipsum！

## 其他工具

我们将使用 [Koa](https://koajs.com/#introduction) 来创建服务器。它是“Express 背后的团队设计的一个新的 web 框架”。实际上，它有一个不同的中间件实现，并且比 express 装载的东西少。如果你想要更多的信息，Koa 的知识库中有一个[的比较](https://github.com/koajs/koa/blob/master/docs/koa-vs-express.md)。

对于 GraphQL 实现，我们将使用 [Apollo 服务器](https://www.apollographql.com/docs/apollo-server/)。这似乎是 GraphQL 最受欢迎的服务器实现，它与 Koa 非常契合，因为有一个 [apollo-server-koa](https://github.com/apollographql/apollo-server/tree/master/packages/apollo-server-koa) 包存在，并且已经由 apollo 服务器团队实现。

最后，我们将使用众所周知的 [cross-env](https://github.com/kentcdodds/cross-env) 包来设置环境变量，而不管您使用的是什么平台。

## 设置服务器

首先，我们创建一个具有以下结构的文件夹:

```
.
├── package.json
└── src
    └── index.js 
```

Enter fullscreen mode Exit fullscreen mode

对于这篇文章，我们将使用`yarn`，但是你也可以使用`npm`。用`init`命令初始化 package . JSON:

```
yarn init 
```

Enter fullscreen mode Exit fullscreen mode

安装以下依赖项:

```
yarn add apollo-server-koa cross-env faker graphql koa 
```

Enter fullscreen mode Exit fullscreen mode

添加一个执行我们的`src/index.js`文件的`start`脚本:

```
{  "scripts":  {  "start":  "node src/index.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在`src/index.js`文件中，我们实例化了一个新的 Koa 应用程序:

```
const Koa = require('koa');

const app = new Koa();

app.listen({port: 4000}, () =>
  console.log(`🚀 Server ready at http://localhost:4000`),
); 
```

Enter fullscreen mode Exit fullscreen mode

运行您的`start`脚本。您应该在控制台输出中看到上面示例中的`console.log`消息。

## 创建 GraphQL 端点

是时候实现我们的 GraphQL 端点了。假设我们处在一个 API 还没有实现的情况下。这个 API 应该公开一个包含名字和姓氏的人员列表。我们将定义一个模式，并通过`apollo-server-koa`在`/graphql`端点上访问它。

从`apollo-server-koa` :
导入`ApolloServer`和`gql`

```
const {ApolloServer, gql} = require('apollo-server-koa'); 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了一个查询，该查询返回一个人员列表和人员类型本身:

```
const typeDefs = gql`
  type Person {
    lastname: String
    firstname: String
  }

  type Query {
    persons: [Person]
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

由于 API 还不存在，我们抛出一个错误通知用户他不能使用这个查询:

```
const resolvers = {
  Query: {
    persons: () => {
      throw Error('Not yet implemented');
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们用我们的类型定义和查询解析器实例化了一个 Apollo 服务器:

```
const server = new ApolloServer({
  typeDefs,
  resolvers,
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，将之前创建的 GraphQL 中间件应用到 Koa 应用程序:

```
server.applyMiddleware({app}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在使用命令`yarn start`启动服务器，并在浏览器中打开 url `http://localhost:4000/graphql`。你应该会看到 [GraphQL IDE](https://github.com/prisma/graphql-playground) 的漂亮界面。

[![GraphQL IDE](img/9cc64ca9725b2ff07906f00811034da5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sqbW6EgK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/frinyvonnick/graphql-faker-mocks/master/assets/GraphQLIDE.png)

如果您键入一个查询来检索人员列表及其数据:

```
{
  persons {
    firstname,
    lastname
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将导致出现以下错误:

```
{
  "errors": [
        {
            "message": "Not yet implemented",
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用 faker.js 嘲弄它

这个错误不是预期的结果。当我们的服务器被嘲笑时，我们需要随机的真实数据。为了实现这一点，我们需要用另一个生成假数据的解析器来覆盖抛出错误的默认解析器。

为此，我们将把`NODE_ENV`环境变量设置为`mock`,以确定 Apollo 服务器应该遵循的行为。我们将通过在我们的`package.json`中添加一个`mock`脚本来实现，该脚本使用`cross-env`设置`NODE_ENV`变量，并调用`start`脚本:

```
{  "scripts":  {  "start":  "node src/index.js",  "mock":  "cross-env-shell NODE_ENV=mock yarn start"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

Apollo server 的选项中有一个`mocks`属性，它接受一个布尔值或一个带有模拟解析器的对象。首先，如果`NODE_ENV`等于`mock` :
，我们将把它设置为`true`

```
const server = new ApolloServer({
  typeDefs,
  resolvers,
  mocks: process.env.NODE_ENV === 'mock' ? true : false,
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这一步，如果您在 GraphQL IDE 中重新执行您的查询，您将得到第一个结果:

```
{  "data":  {  "persons":  [  {  "firstname":  "Hello World",  "lastname":  "Hello World"  },  {  "firstname":  "Hello World",  "lastname":  "Hello World"  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是不太现实。要添加 faker，我们需要实现自己的模拟解析器，并将其传递给 Apollo 服务器。在模拟解析器中，查询属性必须是返回带有解析器定义的对象的函数。在我们的查询解析器中，我们返回两个人的数组。

方法被组织在名称空间中。我们将使用包含如下方法的`name`名称空间:

*   西方人名的第一个字
*   姓
*   jobTitle
*   标题
*   ...

你猜对了，我们将使用`firstName`和`lastName`方法为我们两个人生成随机数据:

```
const mockResolvers = {
  Query: () => ({
    persons: () => [
      {
        firstname: name.firstName(),
        lastname: name.lastName(),
      },
      {
        firstname: name.firstName(),
        lastname: name.lastName(),
      },
    ],
  }),
};

// ...

const server = new ApolloServer({
  typeDefs,
  resolvers,
  mocks: process.env.NODE_ENV === 'mock' ? mockResolvers : false,
}); 
```

Enter fullscreen mode Exit fullscreen mode

在 GraphQL IDE 中执行您的查询，现在您已经有了如下好的数据:

```
{  "data":  {  "persons":  [  {  "firstname":  "Mélissa",  "lastname":  "Mathieu"  },  {  "firstname":  "Camille",  "lastname":  "Richard"  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金

这里有一个方法，我用它来为我模拟的解析器生成随机大小的数组:

```
const randomArray = (min, max, callback) => {
  const size = random.number({min, max});
  return Array.from({length: size}, callback);
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以像这样使用这个方法重构前面的解析器:

```
const mockResolvers = {
  Query: () => ({
    persons: () =>
      randomArray(2, 6, () => ({
        firstname: name.firstName(),
        lastname: name.lastName(),
      })),
  }),
}; 
```

Enter fullscreen mode Exit fullscreen mode

我用本文中介绍的所有源代码创建了一个[存储库](https://github.com/frinyvonnick/graphql-faker-mocks)。感谢反馈🙏有任何问题请发微博给我[@ yvonickfrin](https://twitter.com/YvonnickFrin)！