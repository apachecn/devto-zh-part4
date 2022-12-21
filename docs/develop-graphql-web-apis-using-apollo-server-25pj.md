# 使用 Apollo Server 开发 GraphQL Web APIs

> 原文：<https://dev.to/donvitocodes/develop-graphql-web-apis-using-apollo-server-25pj>

我一直在学习 GraphQL，因为它比 REST APIs 更灵活。GraphQL 最初由脸书在 2012 年开发和使用。2018 年，GraphQL 项目移至 GraphQL 基金会(Linux 基金会下)。

与 REST 相比，我想目前为止我最喜欢的一个明显的主要优点是能够只检索应用程序需要的数据字段。在构建 GraphQL APIs 时，可以从一些有趣的框架开始。

如果你想从头开始构建，你可以从我所知道的这 3 个服务器框架中选择。

阿波罗服务器[https://apolograpql . com/docs/Apollo-server](https://apollographql.com/docs/apollo-server)
express graph QL server[https://github . com/graph QL/express-graph QL](https://github.com/graphql/express-graphql)[棱柱【https://www . prisma . io/with-graph QL】](https://www.prisma.io/with-graphql)

我选择 Apollo 作为这个例子，因为 Apollo 是 GraphQL 领域的先驱之一。此外，代码非常简单。在这个例子中，我们将使用 NodeJS。我在这个例子中使用的是 node v11.4.0。所以它开始了...

下面是 Glitch 中的源代码。随意混音！:)

[https://glitch.com/embed/#!/embed/donvito-graphql-server-apollo-example?path=index.js](https://glitch.com/embed/#!/embed/donvito-graphql-server-apollo-example?path=index.js)

这个例子的所有源代码都可以在我的 github repo 中找到。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [【多瑙河】](https://github.com/donvito) / [【图 QL-server-Apollo-example】](https://github.com/donvito/graphql-server-apollo-example)

### 下面是一个如何使用 Apollo GraphQL 服务器的简单示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# graph QL-server-Apollo-示例

我已经使用 Apollo GraphQL server 创建了一个 GraphQL 服务器的示例。我希望它能对用阿波罗[https://www.apollographql.com/docs/apollo-server/](https://www.apollographql.com/docs/apollo-server/)学习 GraphQL 的人有用

## 安装依赖项

```
npm install 
```

## 运行示例

```
node index.js 
```

## 使用 Glitch 部署

您可以在 glitch 中运行服务器代码。经测试，它工作正常。您可以将存储库分支到 github 帐户中，并在 glitch 中导入 repo。Glitch 免费托管 nodejs 代码。

## 以下是本示例中的完整代码:

```
const { ApolloServer, gql } = require('apollo-server')
const typeDefs = gql`
  type Job {
    id: Int
    position: String
    company: String
    description: String
    location: String
    employmentType: String
    skillsRequired: [String]
  }

  type Query {
    job(id: Int!): [Job],
    jobs: [Job]
  }
`;

const jobs = [
  {
    id: 1,
    position: 'Software Engineer',
    company: 'Apple',
    description: 'job description',
    skillsRequired: ['Go', 'GraphQL'],
    location: 'location',
    employmentType: 'full-time',
  },
  {
    id:
```

…</article>

[View on GitHub](https://github.com/donvito/graphql-server-apollo-example)

完整博文在此
[http://www . melvinvivas . com/graph QL-API-using-Apollo-server-example](http://www.melvinvivas.com/graphql-api-using-apollo-server-example)