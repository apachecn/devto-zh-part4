# 用 Next.js，Prisma 2 和 Docker 创建一个 Fullstack 博客应用程序-第二部分配置前端

> 原文：<https://dev.to/codemochi/create-a-fullstack-blog-app-with-next-js-prisma-2-and-docker-part-ii-configure-the-frontend-14oj>

在这篇文章中，我们从第一部分的[处继续。如果你还没有看过第一篇文章，你应该先看看。提醒一下，我们已经构建了样板前端和后端 web 服务器，并对它们进行了 dockerized。在这篇文章中，我们将配置我们的前端服务器，用 Apollo 把它和我们的后端服务器连接起来，我们将为一个漂亮的网站布局添加 Ant 设计。](https://www.codemochi.com/blog/2019-07-08-prisma-2-nextjs-docker/)

# 目录:

**第一部分-设置回购并配置后端**

1.  创建基础回购
2.  创建 Next.js 前端
3.  创建样板 Prisma 2 后端
4.  将我们的网络应用归档

a.创建一个 docker 合成文件

b.为每个容器添加 docker 文件

1.  配置后端

a.将数据库从 SQLite 切换到 MySQL

b.移除未使用的后端样板代码

c.更新 Prisma Nexus 中的后端查询

1.  验证我们的 Docker-Compose 集群工作正常

**第二部分-配置前端(本岗位)**

1.  添加 GraphQL 片段、查询和解析器
2.  添加 GraphQL-Codegen 以生成 React 组件
3.  添加 Apollo 并为 Next.js 创建 HOC
4.  将 React-Apollo 添加到项目根
5.  安装 Antd 为一个美丽的网站布局

**第三部分-构建用户界面**

1.  创建主布局
2.  创建用户布局组件
3.  创建注册用户组件
4.  创建提要布局组件
5.  创建新的拔模组件
6.  创建发布草稿按钮组件
7.  创建删除帖子按钮组件

就像以前一样-一定要检查整个过程的视频的结尾。

**第二部分-配置前端**

**1。添加我们的 GraphQL 片段、查询和解析器**

这篇文章前半部分的目标是使用代码生成为我们创建最难的 React 组件。为了做到这一点，我们需要首先指定我们的前端将使用的所有查询和变异。这将包括关于需要哪些输入参数以及我们希望从请求中获得哪些字段的信息。我们将使用 graphql 文件创建所有这些。

*添加片段*

首先，为了鼓励代码重用，让我们为 Post 和 User 对象创建片段:

`/frontend/graphql/fragments/post.gql`

```
fragment  PostFragment  on  Post  {  id  published  title  content  published  } 
```

`/frontend/graphql/fragments/user.gql`

```
fragment  UserFragment  on  User  {  id  name  email  } 
```

*添加查询*

我们可以在查询和变异中使用这些片段。让我们首先创建我们的查询:

`/frontend/graphql/queries/feed.gql`

```
#import from '../fragments/post.gql'  query  feedQuery($published:  Boolean!)  {  feed(published:  $published)  {  ...PostFragment  }  } 
```

`/frontend/graphql/queries/post.gql`

```
#import from '../fragments/post.gql'  query  postQuery($id:  ID!)  {  post(where:  {  id:  $id  })  {  ...PostFragment  }  } 
```

`/frontend/graphql/queries/users.gql`

```
#import from '../fragments/user.gql'  query  usersQuery  {  users  {  ...UserFragment  }  } 
```

*添加突变*

现在让我们创造我们的突变:

`/frontend/graphql/mutations/createDraft.gql`

```
#import from '../fragments/post.gql'  mutation  createDraftMutation(  $title:  String!  $content:  String!  $authorEmail:  String!  )  {  createDraft(title:  $title,  content:  $content,  authorEmail:  $authorEmail)  {  ...PostFragment  }  } 
```

`/frontend/graphql/mutations/deleteOnePost.gql`

```
#import from '../fragments/post.gql'  mutation  deleteOnePost($id:  ID!)  {  deleteOnePost(where:  {  id:  $id  })  {  ...PostFragment  }  } 
```

`/frontend/graphql/mutations/publish.gql`

```
#import from '../fragments/post.gql'  mutation  publishMutation($id:  ID!)  {  publish(id:  $id)  {  ...PostFragment  }  } 
```

`/frontend/graphql/mutations/signupUser.gql`

```
#import from '../fragments/user.gql'  mutation  signupUserMutation($name:  String!,  $email:  String!)  {  signupUser(data:  {  name:  $name,  email:  $email  })  {  ...UserFragment  }  } 
```

*2。将 Graphql-Codegen 添加到前端*

Graphql-Codegen 将接受我们的 Graphql 查询、突变和片段，并针对我们的后端服务器进行查询，以创建一个包含 React 组件的生成文件，用于我们可以用后端服务器执行的所有可能的 Apollo 操作。

首先安装 codegen 工具:

```
npm install --save-dev @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typescript-react-apollo graphql 
```

接下来，我们必须添加 codegen 文件，该文件包含代码生成行为的配置:

`frontend/codegen.yml`

```
overwrite: true
schema: 'http://backend:4000/'
documents: graphql/**/*.gql
generates:
  generated/apollo-components.tsx:
    config:
      withHOC: false
      withComponent: true
    plugins:
      - 'typescript'
      - 'typescript-operations'
      - 'typescript-react-apollo' 
```

最后，将一个 npm 脚本添加到我们根目录下的 package.json 文件中:

`/package.json`

```
"generate":  "docker exec -it frontend npm run generate", 
```

还有这个 npm 脚本到你的`frontend/package.json` :

```
"generate":  "gql-gen --config codegen.yml" 
```

现在从根项目运行`npm run generate`。我们可以看到，从根调用 generate 脚本将在我们的前端容器中执行一个 npm run 脚本调用，它将调用 gql-gen 工具。

由于我们在我们的前端应用程序文件夹和我们电脑中的前端文件夹之间创建了一个卷，docker 容器中生成的任何文件都将到达主机。正是由于这个原因，您应该会看到现在有一个新文件`frontend/generated/apollo-components.tsx`，它包含所有的 typescript 类型、graphql 文档和 react 组件。它几乎有 300 行代码，所以它是如此之好，我们不必去创建它。如果您曾经向前端的 graphql 文件夹添加新文件，请确保再次运行 generate，以便为您重新生成该文件。

*3。安装 Apollo 并为 Next.js 创建 HOC*

在前端目录下安装以下库:

```
npm install --save apollo-boost isomorphic-unfetch react-apollo 
```

创建`frontend/utils/init-apollo.js`和`frontend/utils/with-apollo-client.js`文件。

`frontend/utils/init-apollo.js`

```
import { ApolloClient, InMemoryCache, HttpLink } from 'apollo-boost'
import fetch from 'isomorphic-unfetch'

let apolloClient = null

function create(initialState) {
  // Check out https://github.com/zeit/next.js/pull/4611 if you want to use the AWSAppSyncClient
  const isBrowser = typeof window !== 'undefined'
  return new ApolloClient({
    connectToDevTools: isBrowser,
    ssrMode: !isBrowser, // Disables forceFetch on the server (so queries are only run once)
    link: new HttpLink({
      uri: isBrowser ? 'http://localhost:4000' : 'http://backend:4000', // Server URL (must be absolute)
      credentials: 'same-origin', // Additional fetch() options like `credentials` or `headers`
      // Use fetch() polyfill on the server
      fetch: !isBrowser && fetch,
    }),
    cache: new InMemoryCache().restore(initialState || {}),
  })
}

export default function initApollo(initialState) {
  // Make sure to create a new client for every server-side request so that data
  // isn't shared between connections (which would be bad)
  if (typeof window === 'undefined') {
    return create(initialState)
  }

  // Reuse client on the client-side
  if (!apolloClient) {
    apolloClient = create(initialState)
  }

  return apolloClient
} 
```

`frontend/utils/with-apollo-client.js`

```
import React from 'react'
import initApollo from './init-apollo'
import Head from 'next/head'
import { getDataFromTree } from 'react-apollo'

export default App => {
  return class Apollo extends React.Component {
    static displayName = 'withApollo(App)'
    static async getInitialProps(ctx) {
      const { Component, router } = ctx

      let appProps = {}
      if (App.getInitialProps) {
        appProps = await App.getInitialProps(ctx)
      }

      // Run all GraphQL queries in the component tree
      // and extract the resulting data
      const apollo = initApollo()
      if (typeof window === 'undefined') {
        try {
          // Run all GraphQL queries
          await getDataFromTree(
            <App
              {...appProps}
              Component={Component}
              router={router}
              apolloClient={apollo}
            />
          )
        } catch (error) {
          // Prevent Apollo Client GraphQL errors from crashing SSR.
          // Handle them in components via the data.error prop:
          // https://www.apollographql.com/docs/react/api/react-apollo.html#graphql-query-data-error
          console.error('Error while running `getDataFromTree`', error)
        }

        // getDataFromTree does not call componentWillUnmount
        // head side effect therefore need to be cleared manually
        Head.rewind()
      }

      // Extract query data from the Apollo store
      const apolloState = apollo.cache.extract()

      return {
        ...appProps,
        apolloState,
      }
    }

    constructor(props) {
      super(props)
      this.apolloClient = initApollo(props.apolloState)
    }

    render() {
      return <App {...this.props} apolloClient={this.apolloClient} />
    }
  }
} 
```

这两个文件是样板代码，取自与 Apollo 和 GraphQL 一起工作的[Next.js samples](https://github.com/zeit/next.js/tree/canary/examples/with-apollo)——第一个文件创建了一个函数，它将以两种不同的方式连接到我们的后端服务器，这取决于它是在 next . js 服务器上发生的预渲染步骤中，还是在用户的浏览器中。

对原始样板代码所做的唯一更改是，在 create HttpLink 中，如果我们是浏览器，我们将连接到服务器上的`http://localhost:4000`，但是如果我们在前端 docker 容器中，我们实际上将连接到`http://backend:4000`。这是因为 docker-compose 为我们处理网络，所以我们不必知道后端容器在 docker 网络中的实际 IP 地址——我们可以简单地通过 DNS 名称(即我们的容器名称)来引用它，docker 为我们处理网络。整洁！

```
uri: isBrowser ? 'http://localhost:4000' : 'http://backend:4000', 
```

*4。将 React-Apollo 添加到下一个项目的根中。*

既然我们已经创建了 withApolloClient 特设，我们可以在 _app.tsx 文件中使用它。这是 pages 文件夹中的一个特殊文件，将在 Next.js 站点的每个页面上运行。这正是我们需要的，以确保我们有阿波罗访问无处不在。

创建一个新文件`frontend/pages/_app.tsx`

```
import App, { Container } from 'next/app'
import React from 'react'
import withApolloClient from '../utils/with-apollo-client'
import { ApolloProvider } from 'react-apollo'

class MyApp extends App {
  render() {
    // @ts-ignore
    const { Component, pageProps, apolloClient } = this.props
    return (
      <Container>
        <ApolloProvider client={apolloClient}>
          <Component {...pageProps} />
        </ApolloProvider>
      </Container>
    )
  }
}

export default withApolloClient(MyApp) 
```

*5。安装 Antd 获得漂亮的网站布局*

Ant Design 是一个流行的 React UI 框架，我们将在这个项目中使用。它就像 Bootstrap，但我认为它更适合 React 环境，因为你不必为模态弹出窗口安装 jQuery，它通常看起来超级干净，不像其他任何网站。首先我们需要把它安装在我们的前端文件夹:

```
npm install --save antd 
```

然后我们需要将 css 样式添加到`_app.tsx`文件中。只需将其添加到依赖项导入列表的底部:

```
import 'antd/dist/antd.css' 
```

在这个博客文章系列的第 3 部分中，我们将展示如何使用这些 Ant 设计组件来构建我们所有的 React 组件，所以请务必回来查看我们的最后一期文章。

第二部分视频系列:

*为 Next.js 创建查询、突变和片段*
[https://www.youtube.com/embed/xPWVVOisuUw](https://www.youtube.com/embed/xPWVVOisuUw)

*添加 GraphQL 代码生成器*
[https://www.youtube.com/embed/XciDMfOhTA4](https://www.youtube.com/embed/XciDMfOhTA4)

*添加阿波罗到 Next.js*
[https://www.youtube.com/embed/FMHAwWO0DUc](https://www.youtube.com/embed/FMHAwWO0DUc)

*将阿波罗提供者添加到 Next.js*
[https://www.youtube.com/embed/cnsLxx9dSIM](https://www.youtube.com/embed/cnsLxx9dSIM)

*添加蚂蚁设计到 Next.js*
[https://www.youtube.com/embed/3aMIOon-Lf0](https://www.youtube.com/embed/3aMIOon-Lf0)

那里还有更多！点击这里给我们你的电子邮件，我们会在发布新内容时通知你。我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-07-10-prisma-2-nextjs-docker/) 。