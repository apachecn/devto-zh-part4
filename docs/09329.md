# 使用 Hasura 远程连接将 Postgres 数据与 FaunaDB 无服务器连接

> 原文：<https://dev.to/hasurahq/joining-postgres-data-with-faunadb-serverless-using-hasura-remote-joins-2n6f>

## TL；速度三角形定位法(dead reckoning)

使用 Hasura 远程连接将 Postgres 数据库中的数据与 FaunaDB 中的无服务器数据连接起来。

这篇文章是我们的[远程连接](https://blog.hasura.io/remote-joins-a-graphql-api-to-join-database-and-other-data-sources/)(在预览版中可用)系列的一部分。Hasura 中的远程连接允许您跨表和远程数据源连接数据。如果你想在我们发布时得到通知，你可以在这里注册[。前往](https://hasura.io/remote-joins)[公关](https://github.com/hasura/graphql-engine/pull/2395)查看更多细节，阅读预览文件，并尝试一个你可以旋转的测试 Hasura 图像。跳到我们的[不和谐](https://discord.gg/hasura)或者评论 [github](https://github.com/hasura/graphql-engine) ，让我们知道你的想法！

[![](img/1589ee2c8025e6588ea817c00ede2eb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jvnl1POe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/08/faunadb-banner.png)

FaunaDB 是一个面向现代应用的无服务器云数据库。它旨在关注快速部署、日常操作的简单性和开发人员的易用性。它提供了一个 [GraphQL API](https://docs.fauna.com/fauna/current/start/graphql) ，可以使用远程连接与 Hasura 连接。

在这个例子中，我们将看看如何将 FaunaDB 中的模式与 Hasura 中的现有数据连接起来。假设在 Hasura 中有一个包含列`id`和`name`的`users`表，让我们开始吧。

### 在 FaunaDB 中创建 GraphQL 模式

*   登录[FaunaDB Cloud](https://dashboard.fauna.com/accounts/login)——这是管理你的数据库和安全的仪表板。
*   创建新的数据库来存储数据。我们把它命名为`hasura`
*   创建一个 GraphQL 模式。让我们创建一个简单的模式来管理用户的待办事项。[下载下面的模式](https://graphql-engine-cdn.hasura.io/assets/blog/schema.gql)

```
type Todo {
   title: String!
   completed: Boolean!
   userId: Int!
}

type Query {
   allTodos: [Todo!]
   todosByUser(userId: Int): [Todo!]
} 
```

*   通过点击左侧边栏导航栏上的`GraphQL`导入您的 GraphQL 模式，并选择您在上面下载的 GraphQL 模式文件。一旦导入成功，您应该看到 GraphQL Playground 开始使用 API。

### 在 FaunaDB 中插入样本数据

让我们为上面的模式插入样本数据。在 GraphQL Playground 中，执行这个变异来插入一个 todo。

```
mutation CreateATodo {
   createTodo(data: {
   title: "Build an awesome app!"
   completed: false
   userId: 1
   }) {
       title
       completed
   }
} 
```

这将在 userId 1 中插入一个 todo 标记。

### 获取访问密钥

GraphQL 端点需要使用特定的 FaunaDB 数据库进行身份验证。我们需要创建一个角色为`Server Read-Only`的新密钥

[![](img/e7b5412f01f283416d3ddf0e95283627.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kmnjh_Tn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/08/create-faunadb-key.png)

生成的秘密必须作为 HTTP Basic `Authorization`头提供，编码为 Base64 字符串。例如，如果你的 FaunaDB secret 是`fnADMxRzydATDKibGAciQlNQWBs-HJdpJS1vJaIM`，你可以这样编码:

```
echo -n "fnADMxRzydATDKibGAciQlNQWBs-HJdpJS1vJaIM:" | base64
Zm5BRE14Unp5ZEFUREtpYkdBY2lRbE5RV0JzLUhKZHBKUzF2SmFJTTo= 
```

尾随冒号(`:`)是必需的。

那么你的`Authorization`头应该是这样的:

```
Authorization: "Basic Zm5BRE14Unp5ZEFUREtpYkdBY2lRbE5RV0JzLUhKZHBKUzF2SmFJTTo=" 
```

## 添加 FaunaDB 作为远程模式

为了能够通过 Hasura 查询 FaunaDB 数据，需要使用 Hasura 控制台将其添加为远程模式。

*   GraphQL API 端点是:

```
https://graphql.fauna.com/graphql 
```

*   现在复制密钥(base64 编码)并在授权头中使用它，如下所示:

```
Authorization: Basic <base64 encoded key> 
```

*   在 Hasura 控制台中，转到 Remote Schemas 并输入带有上述端点的 GraphQL 服务器 URL。在“附加标题”下，输入如上所述的带有密码的授权标题。

[![](img/a371e06f98e225575aa6b4da53ebe23d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kCEVug0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/08/add-remote-schema-faunadb.png)

现在，让我们转到`users`表- >关系并添加远程关系`todos`。

[![](img/c19bea4236e83412b1a1523b0de04825.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aUomO9UD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/08/remote-relationship-faunadb.png)

在`todosByUser`的配置下，我们声明一个过滤器说:

```
userId: From column -> id 
```

这确保了在用户中查询 todo 时，我们只获得由被查询的用户编写的相关 todo。

现在，在单个 API 调用中获取这些数据的 GraphQL 查询如下所示:

```
query {
  users {
    id
    name
    todos {
      data {
        title
        completed
      }
    }
  }
} 
```

注意，嵌套查询`todos`来自 FaunaDB，它将应用 users.id = todos.data.userId 的过滤器，从而只给出用户编写的 todos。