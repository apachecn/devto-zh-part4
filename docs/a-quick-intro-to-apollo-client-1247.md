# Apollo 客户端快速介绍

> 原文：<https://dev.to/eveporcello/a-quick-intro-to-apollo-client-1247>

几个月前，我们发布了一篇关于如何从 GraphQL API 获取数据的文章。在其中，我们查看了 [Snowtooth API](https://snowtooth.moonhighway.com) ，这是一个带有真实 GraphQL API 的假滑雪场，我们能够发送查询来获取数据，并发送突变来更改数据。我们都玩得很开心。

现在几个月过去了，我们的头发长了一点。我们的胡子有点多了。现在我们可能会想:是时候加入阿波罗客户端了吗？或者更重要的是，你为什么想要合并 Apollo 客户端？

Apollo 客户端处理网络请求和高效缓存。它将通过创建一个链接来处理 GraphQL API 的网络接口。缓存将保存查询及其结果对象。使用 REST 的一个巨大好处是可以轻松处理缓存。使用 REST，您可以将请求的响应数据保存在用于访问该请求的 URL 下的缓存中。例如，如果 Snowtooth 是一个 REST API，那么您可以转到 lifts 路径`/lifts`来获取 lift 数据，然后将数据缓存在该 URL 下。

使用 GraphQL，我们处理的是单个端点，因此在我们构建快速、高效的应用程序时，拥有本地化的缓存解决方案是必不可少的。我们可以创建自己的缓存，但是依靠经过审查的 Apollo 客户端是一个很好的起点。

## 阿波罗客户端设置

让我们创建一个简单的 Apollo 客户机，它将从 Snowtooth API 获取数据。

然后，我们将使用 Create React App 设置项目。使用 Apollo Client 并不需要使用 React，但是运行 React 将为我们生成一个项目 shell，在将它扩展到实际应用程序时可能会有用。

通过在终端或命令提示符下运行以下命令开始:

```
npx create-react-app snowtooth-client 
```

`npx`将执行带有项目名称的`create-react-app`命令，在本例中为`snowtooth-client`。然后确保你在`snowtooth-client`目录下，启动应用:

```
cd snowtooth-client
npm start 
```

这将在`localhost:3000`上运行启动程序。接下来，我们将安装一些依赖项:

```
npm i apollo-client graphql 
```

我们将对迷你应用程序进行的所有更改都将发生在`src/index.js`文件中。第一个变化是我们将突出显示文件中的所有内容并删除它。然后，我们将创建客户端，并将客户端的任何内容记录到控制台:

```
import { ApolloClient } from "apollo-client";

const client = new ApolloClient();

console.log(client); 
```

此时，我们应该会看到一个错误:`Uncaught TypeError: Cannot read property 'cache' of undefined`。还记得我们之前说过 Apollo Client 给了我们两样东西:到 GraphQL 数据的链接和缓存吗？这是在设置客户端构造函数时需要提供给它的两件事情。从安装几个更有用的包开始:

```
npm i apollo-link-http apollo-cache-inmemory 
```

这些包将帮助我们连接到我们的 GraphQL API，并设置一个本地缓存。安装完成后，我们将在`index.js`文件中导入并使用它们。让我们从进口开始:

```
import { createHttpLink } from "apollo-link-http";
import { InMemoryCache } from "apollo-cache-inmemory"; 
```

然后我们将在客户端构造函数中使用这些助手:

```
const client = new ApolloClient({
  link: createHttpLink({ uri: "https://snowtooth.moonhighway.com" }),
  cache: new InMemoryCache()
}); 
```

`createHttpLink`使用在`uri`键上提供的 GraphQL API 接收一个对象，并通过 http 连接从 GraphQL API 获取 GraphQL 结果。`InMemoryCache`是 Apollo 客户端 2.0 的默认缓存实现。

我们可以再次检查`localhost:3000`并打开控制台。这是现在登录客户端。这里还没有太多，但我们可以在这里看到一些熟悉的值，如`cache`和`link`。

[![Client Log](img/426a4352d3c9159836f2f92a9c806acc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VlMX7nsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonhighway.com/static/ccf983072f960061d8fb18f67841b929/0fafd/clientlog.png)

此时，我们不会发送请求。为了向 GraphQL 端点发送请求(查询)，我们需要合并最后一个包:`graphql-tag`。

```
npm install graphql-tag 
```

安装完成后，我们将从`graphql-tag`导入一个名为`gql`的函数，并将我们要发送给 API 的查询封装在这个标签中:

```
import gql from "graphql-tag";

const query = gql`
  query {
    allLifts {
      name
    }
  }
`; 
```

包装查询的标签函数`gql`将解析查询字符串，并将其转换成 AST，一个抽象的语法树。AST 是查询字符串作为对象的表示。定义好之后，我们将使用`client.query`函数向 GraphQL API 发送一个查询:

```
client
  .query({
    query
  })
  .then(console.log); 
```

调用`client.query`将把查询发送给在我们的客户机实例化中定义的 API。运行此命令后，您应该会看到记录到控制台的 JSON 响应:

[![Object Response](img/9b11eed973ec9677503cc07071aa3a79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EfPAPEvu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonhighway.com/static/97a91f1e6ee73e71d49e7da96e61dbdc/db64a/objresponse.png)

这个对象为我们提供了对名为`data`的键的查询的所有数据。我们还可以看到数据是否正在加载，请求是否过时，以及网络状态代码。如果成功，网络状态代码将是 7。

我们可以通过控制台日志消息了解的另一件事是缓存:

```
console.log("Cache", client.cache); 
```

这将显示整个客户端缓存对象。我们还可以使用
深入研究数据响应

```
console.log("Cache", client.extract()); 
```

数据缓存在一个名为`ROOT_QUERY`的键上。然后我们看到数据被添加到查询值的索引中。例如，如果我们请求`allLifts`，第一个 lift 将在`allLifts.0`缓存，第二个 lift 将在`allLifts.1`缓存，以此类推。

如果你想在一个更好的用户界面中查看缓存的状态，我推荐安装 [Apollo Dev Tools Chrome 扩展](https://chrome.google.com/webstore/detail/apollo-client-developer-t/jdkknkkbebbapilgoeccciglkfbmbnfm?hl=en-US)。任何时候你在一个使用 Apollo 客户端的页面上，你都可以打开开发工具，这将使你可以看到缓存，这比将这些细节记录到控制台要好一些:

[![DevTools](img/26e1f92abf48dd0c3b69c85fa8a48779.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vKkRly_c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonhighway.com/static/50fb33756f6f30da7c270ec6c6c141db/20783/devtools.png)

您可以看到缓存的状态这一事实很酷，但是让 GraphiQL explorer 可用也非常有用。如果您正在查看的页面有一个 Apollo 客户机的实例，您将能够使用 GraphiQL 来自省模式并发送查询、变更和订阅。黑仔。

Apollo 客户端不需要你使用任何特定的 UI 库。我们在本文中所做的一切都使用了优秀的传统 JavaScript。如果你想了解更多关于如何集成 Apollo Client 和 React 的信息，请查看我们的另一篇关于 [Apollo React Hooks](https://moonhighway.com/apollo-hooks) 的文章！