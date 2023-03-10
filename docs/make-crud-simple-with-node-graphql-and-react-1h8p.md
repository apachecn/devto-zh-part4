# 用 Node、GraphQL 和 React 简化 CRUD

> 原文：<https://dev.to/bnevilleoneill/make-crud-simple-with-node-graphql-and-react-1h8p>

**由[avathika meenakshi](https://blog.logrocket.com/author/avanthikameenakshi/)T3]撰写**

GraphQL 通过将所有请求抽象到一个端点来降低构建 API 的复杂性。与传统的 REST APIs 不同，它是声明性的；无论请求什么都会被返回。

当然，并不是所有的项目都需要 graph QL——它仅仅是一个整合数据的工具。它有定义良好的模式，所以我们肯定不会过度提取。但是如果我们已经有了一个稳定的 RESTful API 系统，我们依赖于来自单一数据源的数据，我们就不需要 GraphQL 了。

例如，假设我们正在为自己创建一个博客，我们决定在一个 MongoDB 数据库中存储、检索和交流数据。在这种情况下，我们没有做任何架构上复杂的事情，也不需要 GraphQL。

另一方面，假设我们有一个完全成熟的产品，它依赖于来自多个来源的数据(例如，MongoDB、MySQL、Postgres 和其他 API)。在这种情况下，我们应该选择 GraphQL。

例如，如果我们正在为自己设计一个 portfolio 站点，我们想要来自社交媒体和 GitHub 的数据(以显示贡献)，并且我们也有自己的数据库来维护一个博客，我们可以使用 GraphQL 来编写业务逻辑和模式。它将把数据整合为单一的真实来源。

一旦我们有了将正确的数据分发到前端的解析器功能，我们将能够轻松地管理单个数据源中的数据。在本文中，我们将使用 GraphQL 实现简单的端到端 CRUD 操作。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## CRUD 与 graphql-server

### 设置我们的服务器

我们将使用 [express-graphql](https://www.npmjs.com/package/express-graphql) 剥离一个简单的 GraphQL 服务器，并将其连接到 MySQL 数据库。源代码和 MySQL 文件都在这个[仓库](https://github.com/AvanthikaMeenakshi/graphqlReactBoilerplate)中。

GraphQL 服务器构建在模式和解析器之上。作为第一步，我们构建一个模式(定义类型、查询、变异和订阅)。这个模式描述了整个应用程序的结构。

其次，对于模式中定义的内容，我们正在构建各自的解析器来计算和分发数据。解析器将动作与功能进行映射；对于 typedef 中声明的每个查询，我们创建一个解析器来返回数据。

最后，我们通过定义端点和传递配置来完成服务器设置。我们将`/graphql`初始化为应用程序的端点。我们将构建的模式和根解析器传递给`graphqlHTTP`中间件。

除了模式和根解析器，我们还启用了 [GraphiQL](https://www.npmjs.com/package/graphiql) 游乐场。GraphQL 是一个交互式浏览器内 GraphQL IDE，它帮助我们处理我们构建的 GraphQL 查询。

```
var express = require('express');
var graphqlHTTP = require('express-graphql');
var { buildSchema } = require('graphql');

var schema = buildSchema(`
  type Query {
    hello: String
  }
`);

var root = {
  hello: () => "World"
};

var app = express();

app.use('/graphql', graphqlHTTP({
  schema: schema,
  rootValue: root,
  graphiql: true,
}));

app.listen(4000);

console.log('Running a GraphQL API server at localhost:4000/graphql'); 
```

Enter fullscreen mode Exit fullscreen mode

一旦服务器准备就绪，用`node index.js`运行应用程序将在`http://localhost:4000/graphql`启动服务器。我们可以查询 hello 并得到字符串“World”作为响应。

### 连接数据库

我将建立与 MySQL 数据库的连接，如下所示:

```
var mysql = require('mysql');

app.use((req, res, next) => {
  req.mysqlDb = mysql.createConnection({
    host     : 'localhost',
    user     : 'root',
    password : '',
    database : 'userapp'
  });
  req.mysqlDb.connect();
  next();
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以连接多个数据库/源，并在解析器中整合它们。我在这里连接到一个 MySQL 数据库。我在本文中使用的数据库转储在 GitHub 存储库中。

### 用 GraphQL 读写数据

我们使用查询和变异来读取和修改数据源中的数据。在这个例子中，我定义了一个通用的 queryDB 函数来帮助查询数据库。

#### 查询

列出和查看数据的所有 SELECT 语句(或读操作)都放在`type Query` typedef 中。我们在这里定义了两个查询:一个列出数据库中的所有用户，另一个按 id 查看单个用户。

1.  **列出数据:**为了列出用户，我们定义了一个名为`User`的 GraphQL 模式对象类型，它表示我们可以从`getUsers`查询中获取或期望的内容。然后我们定义`getUsers`查询来返回一组用户。
2.  **查看单个记录:**为了查看单个记录，我们将`id`作为我们定义的`getUserInfo`查询的参数。它在数据库中查询该特定 id，并将数据返回给前端。

[![GraphiQL Demo Returning Array Of Users](img/1b0936df12b46e8697eb9ee2db5817a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HthI9cHr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/graphiql-demo.gif%3Fresize%3D730%252C333%26ssl%3D1)

现在我们已经将获取所有记录和按 id 查看记录的查询放在一起，当我们试图从 GraphiQL 中查询用户时，它将在屏幕上列出一组用户！[🙂](https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f642.png)

```
var schema = buildSchema(`
  type User {
    id: String
    name: String
    job_title: String
    email: String
  }
  type Query {
    getUsers: [User],
    getUserInfo(id: Int) : User
  }
`);

const queryDB = (req, sql, args) => new Promise((resolve, reject) => {
    req.mysqlDb.query(sql, args, (err, rows) => {
        if (err)
            return reject(err);
        rows.changedRows || rows.affectedRows || rows.insertId ? resolve(true) : resolve(rows);
    });
});

var root = {
  getUsers: (args, req) => queryDB(req, "select * from users").then(data => data),
  getUserInfo: (args, req) => queryDB(req, "select * from users where id = ?", [args.id]).then(data => data[0])
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 突变

数据库的写操作——创建、更新、删除——通常在突变下定义。GraphQL 引擎以连续的方式执行变异。查询是并行执行的。

1.  创建数据:我们已经定义了一个变体`createUser`，它采用指定的参数在 MySQL 数据库中创建数据。
2.  更新或删除数据:类似于查看记录，更新(`updateUserInfo`)和删除(`deleteUser`)以 id 为参数，修改数据库。

这些函数用一个布尔值来表示是否发生了变化。

```
var schema = buildSchema(`
  type Mutation {
    updateUserInfo(id: Int, name: String, email: String, job_title: String): Boolean
    createUser(name: String, email: String, job_title: String): Boolean
    deleteUser(id: Int): Boolean
  }
`);

var root = {
  updateUserInfo: (args, req) => queryDB(req, "update users SET ? where id = ?", [args, args.id]).then(data => data),
  createUser: (args, req) => queryDB(req, "insert into users SET ?", args).then(data => data),
  deleteUser: (args, req) => queryDB(req, "delete from users where id = ?", [args.id]).then(data => data)
}; 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经设置并排序了服务器端的东西，让我们尝试将后端连接到我们的 React 应用程序。

## CRUD 与 graphql-client

一旦我们有了服务器，创建客户机逻辑来显示和改变数据就很容易了。Apollo 客户端有助于状态管理和缓存。它也是高度抽象和快速的:所有用于检索数据、跟踪加载和错误状态以及更新 UI 的逻辑都由`useQuery`钩子封装。

### 连接到 graphql-server

我创建了一个 CRA 样板文件，并安装了 [GraphQL](https://www.npmjs.com/package/graphql) 、 [apollo-boost](https://www.npmjs.com/package/apollo-boost) 和 [@apollo/react-hooks](https://www.npmjs.com/package/@apollo/react-hooks) 。我们初始化 Apollo 客户机，并让它挂钩作出反应。

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import ApolloClient from 'apollo-boost';
import { ApolloProvider } from '@apollo/react-hooks';

const client = new ApolloClient({
  uri: 'http://localhost:4000/graphql'
});

ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
  document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

### 读取和变异数据

我已经在我的源代码的`Queries`文件夹中管理了所有的 GraphQL 查询。我将使用`useQuery`钩子从服务器请求数据，该钩子构建在 React 钩子 API 之上。它有助于将数据引入 UI。

GraphQL 查询通常包装在`gql`函数中。`gql`帮助将查询字符串转换成查询文档。下面是我们如何在应用程序中定义查询。

```
import { gql } from 'apollo-boost';

export const GET_USERS = gql`
  {
    getUsers {
      id,
      name,
      job_title,
      email
    }
  }
`;

export const VIEW_USERS = gql`
  query ($id: Int){
    getUserInfo(id: $id) {
      id,
      name,
      job_title,
      email
    }
  }
`;

export const ADD_USER = gql`
  mutation($name: String, $email: String, $job_title: String) {
    createUser (name: $name, email: $email, job_title: $job_title)
  }
`;

export const EDIT_USER = gql`
  mutation($id: Int, $name: String, $email: String, $job_title: String) {
    updateUserInfo (id: $id, name: $name, email: $email, job_title: $job_title)
  }
`;

export const DELETE_USER = gql`
  mutation($id: Int) {
    deleteUser(id: $id)
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

一旦设置了`ApolloProvider`,我们就可以从我们的 GraphQL 服务器请求数据。我们将试图进行的查询传递给`useQuery`钩子，它将为我们提供结果。

我做了两个有参数和没有参数的查询，来展示我们应该如何在前端处理查询和变异。`useQuery`为我们跟踪`error`和`loading`状态，并将反映在关联对象中。一旦服务器发送结果，它将通过数据属性反映出来。

```
import React from 'react';
import { useQuery } from '@apollo/react-hooks';
import { GET_USERS, VIEW_USERS } from "./Queries";
import { Card, CardBody, CardHeader, CardSubtitle, Spinner } from 'reactstrap';

function App() {
  const getAllUsers = useQuery(GET_USERS);
  const userInfo = useQuery(VIEW_USERS, { variables: { id: 1 }});
  if (getAllUsers.loading || userInfo.loading) return <Spinner color="dark" />;
  if (getAllUsers.error || userInfo.error) return <React.Fragment>Error :(</React.Fragment>;

  return (
    <div className="container">
      <Card>
        <CardHeader>Query - Displaying all data</CardHeader>
        <CardBody>
          <pre>
            {JSON.stringify(getAllUsers.data, null, 2)}
          </pre>
        </CardBody>
      </Card>
      <Card>
        <CardHeader>Query - Displaying data with args</CardHeader>
        <CardBody>
          <CardSubtitle>Viewing a user by id</CardSubtitle>
          <pre>
            {JSON.stringify(userInfo.data, null, 2)}
          </pre>
        </CardBody>
      </Card>
    </div>
  )
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

与查询类似，变异将使用相同的`useQuery`钩子，并将数据作为变量传递给查询。

```
const deleteMutation = useQuery(DELETE_USER, { variables: { id: 8 }});
const editMutation = useQuery(EDIT_USER, { variables: { id: 9, name: "Username", email: "email", job_title: "job" }});
const createMutation = useQuery(ADD_USER, { variables: { name: "Username", email: "email", job_title: "job" }}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

哒哒！我们只是用 GraphQL 做了端到端的 CRUD 操作。在客户端，引入 React 钩子后，读取和改变数据变得非常简单。Apollo Client 还提供了身份验证、更好的错误处理、缓存和乐观 UI。

订阅是 GraphQL 中另一个有趣的概念。有了这个应用程序作为样板，我们可以继续试验其他类似的概念！

编码快乐！

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[用 Node、GraphQL 和 React](https://blog.logrocket.com/crud-with-node-graphql-react/) 简化 CRUD 最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。