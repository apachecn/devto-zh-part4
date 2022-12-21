# “共享账户”问题

> 原文：<https://dev.to/berolattidiego/the-shared-account-issue-4d8g>

我需要写这个问题。它以某种方式涉及到:

*   GraphQL(阿波罗)
*   NodeJS
*   隐藏物
*   HTTP 标题

**情况**

几个月前我在做这个项目。我正在为一个新网站开发后端。这是第二次使用 Apollo 作为后端工具的一部分(使用 GraphQL 而不是 REST 可以更有效地分离前端和后端的职责，我很喜欢这一点)。一切都很好，直到有一天我发现服务发生了一些奇怪的事情。

出于某种原因，某个特定用户的数据开始向服务中的所有用户显示。我称之为“共享帐户”问题，因为它使服务器认为访问数据的总是同一个用户。

顺便说一下，我之前没有提到的是，用户对服务的认证是通过外部 REST 服务进行的。客户端需要发送一个在 GraphQL 后端服务内部使用的密钥来验证用户。

**任务**

这项任务非常简单。找到这个尴尬的问题并解决它。

**动作**

首先，我确保这个问题不是我想象的一部分，我重复了几次，直到它不可否认。

然后，我试图用一种我每次都可以复制的方式来隔离这个行为。这非常重要，因为它让我能够找到嫌疑犯。

因此，我开始分析与前端的交互(有时问题在于调用服务的方式)，然后只分析与外部客户端的后端行为，最后将日志添加到行为怪异的特定部分。

在客户端中更改身份验证头密钥不会改变任何事情。

重新启动服务器并没有解决问题，只是将共享帐户更改为第一个使用该服务的用户。

然后:我发现问题了！

Apollo 在服务器的缓存中保存了前一个用户的认证。我不知道为什么要保存它，尤其是那个用户。我想了解 apollo 中的缓存是如何工作的，所以我谷歌了一下:“restdata source Apollo remove cache”

(RESTDataSource 是负责缓存的 apollo 类的名称)

第一个链接把我带到了去年的阿波罗号:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 使用 rest data source# 1562](https://github.com/apollographql/apollo-server/issues/1562)时禁用 ApolloServer 中的缓存

[![sharathm89 avatar](img/001f53e0bc73e03c77fb632871e216b6.png)](https://github.com/sharathm89) **[sharathm89](https://github.com/sharathm89)** posted on [<time datetime="2018-08-22T09:17:40Z">Aug 22, 2018</time>](https://github.com/apollographql/apollo-server/issues/1562)

我使用 RESTDataSource 从 REST api 获取数据，但是数据正在被缓存。

由于查询解析器中的数据不断变化，如何禁用缓存。

下面是 node.js 代码。

```
 const app = express();

    const server = new ApolloServer({
      schema: schema,
      context: ({ req }) => ({
        token: req.headers.authorization,
      }),
      dataSources: () => apiDataSources,
    });

    server.applyMiddleware({ app });

    app.listen({ port: 4000 }, () => {
      console.log("Server is at http://localhost:4000/graphql");
    }); 
```

[View on GitHub](https://github.com/apollographql/apollo-server/issues/1562)

那里至少提出了 4 种解决方案:

*   强制类禁用缓存的两种方法
    = >不想这样做，所以我没有尝试。

*   其他检查认证服务的响应头。
    = > Apollo RESTDataSource 使用 HTTP 响应“缓存”头来
    在缓存中存储响应，但是在我的例子中，头响应
    是“无缓存”

*   其他人将 TTL 报头更改为 60 秒或 0 秒或-1 秒。默认情况下，Apollo 使用 24 小时在缓存中存储数据，您可以通过将 TTL 降低到 60 秒来更改这一点。有些人建议使用 0 秒甚至-1 秒，但这是不可靠的。

这些对我都不起作用。

最后，一位用户提到，apollo 中的上下文(包含 RESTDataSource 的对象)是根据请求实例化的。

就是这样！

```
 const dataSources = {
      usersDatasource: new UsersDatasource(),
​      spreadsheetDatasource: new SpreadsheetDatasource(database, logger),
  };

  ...
  introspection: false,
  playground: false,
  dataSources,
  },
  ... 
```

我是这样修复的:

```
 ...
  introspection: false,
  playground: false,
  dataSources: () => {
​    return {
​      usersDatasource: new UsersDatasource(),
​      spreadsheetDatasource: new SpreadsheetDatasource(database, logger),
​    };
  },
  ... 
```

**结果**

我从解决这个问题中学到了很多，我希望你也能学到一些东西。

迭戈（男名）