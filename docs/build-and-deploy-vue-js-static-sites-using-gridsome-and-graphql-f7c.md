# 使用 Gridsome 和 GraphQL 构建和部署 Vue.js 静态站点

> 原文：<https://dev.to/hasurahq/build-and-deploy-vue-js-static-sites-using-gridsome-and-graphql-f7c>

**TL；DR:** 从您现有的 postgres 数据库中为您的 Gridsome 静态站点获取源数据。即时设置。教程/样板文件👉[grid some-postgres-graph QL](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/gridsome-postgres-graphql)

<figure>[![](img/bae67f9aa850b4ad63f2afa69ce69ed0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7BP1Yp3n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/06/Gridsome_blog_1.png)

<figcaption>grid some<-graph QL<-Hasura<-Postgres</figcaption>

</figure>

[Gridsome](https://github.com/gridsome/gridsome) 借助 Vue.js 的力量，轻松构建现代 JAMStack 网站和 pwa，速度快，接受 GraphQL 等自定义数据源。如果您熟悉 React，那么构建静态站点的等效框架应该是 Gatsby。

### 部署 Hasura

[Hasura](https://hasura.io/) 是一个[开源](https://github.com/hasura/graphql-engine)引擎，为您提供新的或现有 Postgres 数据库上的实时 graph QL API，内置支持拼接定制 graph QL API 和在数据库更改时触发 webhooks。

按照[自述文件](https://github.com/hasura/graphql-engine/blob/master/community/sample-apps/gridsome-postgres-graphql/README.md)中的说明部署 Hasura，并创建 app 所需的表格**作者**和**文章**。注意 GraphQL 端点的 Heroku URL。您将在应用程序中对此进行配置。

### 克隆并运行 app

[演示应用](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/gridsome-postgres-graphql)带你构建一个简单的博客应用，在主页列出所有作者，在文章页面列出所有文章。您可以克隆并运行该应用程序进行演示。

```
$ git clone git@github.com:hasura/graphql-engine.git
$ cd graphql-engine/community/sample-apps/gridsome-postgres-graphql
$ yarn install 
```

**注意:**这个应用程序是使用 gridsome-cli 创建的，它生成一个样板 hello world 应用程序。

安装之后，您必须配置上面从 Heroku 获得的 GraphQL 端点。

打开`gridsome.config.js`，配置`url`指向 heroku app graphql 端点。字段名被配置为根级别的`hasura`，以避免与其他数据源冲突。

```
module.exports = {
  siteName: 'Gridsome',
  plugins: [{
      use: '@gridsome/source-graphql',
      options: {
        url: 'http://localhost:8080/v1/graphql',
        fieldName: 'hasura',
        headers: {
          // Authorization: `Bearer ${process.env.AUTH_TOKEN}`,
        },
      },
    }]
} 
```

现在运行 app:

```
yarn start 
```

您应该会看到 Gridsome 应用程序主页如下所示:

<figure>[![](img/a155a447602a0c8dcf816df94d3649b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VbW-lsr---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/06/gridsome-homepage.png)

<figcaption>grid some sample app with graph QL</figcaption>

</figure>

如果您已经向 readme 中提到的作者和文章添加了示例数据，您可以导航到 **/articles** 并在 UI 上查看数据模板。

## 图 QL 数据提取

既然您已经查看了演示应用程序，那么让我们来看看幕后发生了什么。对于这个应用程序，我们需要从 Postgres 获取数据，并在 Vue 组件上将其模板化。要提取的数据很简单；文章和作者列表。

获取所有作者的 GraphQL 查询类似于

```
query {
  hasura {
    author {
      id
      name
    }
  }
} 
```

获取所有文章以及与文章相关的作者的查询如下所示:

```
query {
  hasura {
    article {
      id
      title
      author {
        name
      }
    }
  }
} 
```

上面的查询利用关系来获取文章的相关数据(在本例中是作者)。

基于`pages`目录中的文件自动生成路线。

## 生产构建

静态站点可以使用 gridsome build 生成，它生成 html 和 javascript 包。

运行以下命令来构建资产:

```
yarn build 
```

这将在`dist`目录下为 app 的所有静态页面生成 html、js 和 css 包。

现在，使用命令
为生产应用程序提供服务

```
gridsome serve 
```

这将服务于端口 8080 的生产构建。

## 部署

静态 app 可以免费部署到 **Netlify** 上。点击`New site from git`并为此应用程序选择以下设置。

*   **构建命令:** `gridsome build`
*   **发布目录:** `dist`

## 最后的想法

如果您对 Vue 感兴趣，并且希望构建静态站点，Gridsome 是一个不错的选择。通过自定义数据源支持和与不同 GraphQL 源的模式拼接，它提供了一种强大而灵活的方式来构建现代静态站点。

我已经把样板和教程放在一起，这样你就可以快速开始了！

在 [github](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/gridsome-postgres-graphql) 上查看。

阅读更多关于 [JAMStack](https://jamstack.org) 的内容，了解 CDN 前静态站点的优势。

试玩一下，让我们知道你的想法。如果您有任何问题或遇到任何麻烦，请随时通过 [twitter](https://twitter.com/hasurahq) 、 [github](https://github.com/hasura/graphql-engine) 或我们的 [discord 服务器](https://discord.gg/vBPpJkS)联系我们。