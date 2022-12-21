# 什么是 GraphQL，如何与 React.js 一起使用(第二部分)？

> 原文：<https://dev.to/quickly_react/what-is-graphql-and-how-to-use-with-react-js-part-2-26lh>

[![intersections part 2](img/27b967429334106cc65ba4b86b7033ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q9JSd6zN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1558131716-fe15c66cb56f%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D968%26q%3D80)

欢迎回来！

[![we are back](img/c072c9c8687d1040172e63c2f65b92c1.png)](https://i.giphy.com/media/ASd0Ukj0y3qMM/source.gif)

在我们上一篇[文章](https://dev.to/what-is-graphql-part-1/)中，我们讨论了什么是 GraphQL 以及它是如何工作的。在这一部分中，我们将了解如何将 GraphQL 与 API 包装一起使用。

## 如何将 GraphQL 与 API wrappers 一起使用？

GraphQL 也有两个 API 包装器的实现:

1.  **服务器端**:

[![server wrapper](img/39cc217c4d271b5ef084b14feb5a7fb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RTNZM2YS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./s_w.png)

*OC 解释服务器端包装器的图示*👏

*大家:拥有服务器端 GraphQL API 包装器意味着什么？*

假设您有一个预先存在的 REST API，并且您想将服务器端包装器用于 GraphQL，我们将需要另一个服务器(如图所示),它位于客户端和 REST 服务器通信之间。

*   客户端将访问一个 GraphQL 服务器，而不知道 REST 服务器

*   REST 服务器将只与 GraphQL 服务器通信。

*   GraphQL 服务器将把数据从客户机传送到 REST，并根据来自客户机的`query`发送适当的响应。

*大家:*🤔*这有点令人困惑，这怎么可能呢？*

让我们试着设置一下，看看它是如何工作的。

在上一篇[文章](https://dev.to/what-is-graphql-part-1/)中，我们简要讨论了 GraphQL 服务器(无包装器)的工作原理

这是一个例子:

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

服务器端包装器的当前实现也将与此非常相似。

我们将把它扩展成我们的 GraphQL 服务器包装器。

[![here we go](img/d06f3b62af8867c440534caa0cbabbee.png)](https://i.giphy.com/media/dujg3dNjrf119zBXYD/source.gif)

1.  对于我们的 REST API，我们将使用[https://jsonplaceholder.typicode.com/](https://jsonplaceholder.typicode.com/)，这是一个在线假冒的 REST API。

2.  我们需要设置本地 GraphQL 服务器，它将查询模拟 REST 服务器。

3.  所以把我们的`express-server`设置在`server.js`

```
var express = require('express');
var graphqlHTTP = require('express-graphql');
var schema = require('./schema');
const app = express();

app.use(graphqlHTTP({
    schema,
    graphiql: true,
}));
app.listen(4000); 
```

这一次，我们的 GraphQL 服务器不需要单独的端点，所以如果你去`localhost:4000`你将能够与 graph QL 交互。

1.  我们需要定义我们的`schema.js`

在我们之前的例子中，我们的模式很像`hello: String`，但是在这个例子中，我们有`posts`和`post`，它们分别是一个数组和一个对象。

*   对于帖子，我们可以做:

```
import {
  GraphQLSchema,
  GraphQLObjectType
} from 'graphql'

const BASE_URL = 'https://jsonplaceholder.typicode.com/'

const QueryType = new GraphQLObjectType({
  name: 'Query'
  description: '...',
  fields: () => ({
    post: {
      type: PostType, //needs to be declared

    },
    posts: {
      type: new GraphQLNonNull(new GraphQLList(new GraphQLNonNull(PostType))), //array of posts
    }
  })
});

export default new GraphQLSchema({ //essentially our schema string from previous example
  query: QueryType,
}); 
```

*   所以，现在我们的总体模式已经设置好了，我们需要定义`PostType`并进行 API 调用(更重要的是！😅).

所以让我们开始吧

```
import {
  GraphQLList,
  GraphQLNonNull,
  GraphQLObjectType,
  GraphQLSchema,
  GraphQLString
} from 'graphql'

import fetch from 'node-fetch';

const BASE_URL = 'https://jsonplaceholder.typicode.com';

const QueryType = new GraphQLObjectType({
  name: 'Query'
  description: '...',
  fields: () => ({
    post: {
      type:   PostType //needs to be declared
      args: {
        id: {
          type: GraphQLString
        }
      },
      resolve: (root, args) =>
        fetch( `${BASE_URL}/posts/${id}` ) //API call for posts/1 say.
          .then(res => res.json())
    },    
    posts: {
      type: new GraphQLNonNull(new GraphQLList(new GraphQLNonNull(Post))), //array of posts
      resolve: (root, args) => 
        fetch( `${BASE_URL}/posts` ) //API call for all posts.
          .then(res => res.json())
    },
  })
});

export default new GraphQLSchema({ //essentially our schema string from previous example
  query: QueryType,
}); 
```

现在，我们已经添加了 API 调用。我们需要通过声明我们的类型来结束它。

```
const PostType = new GraphQLObjectType({
  name: 'Post',
  description: '...',
  fields: () => ({
    title: {type: GraphQLString},
    body: {type: GraphQLString},
    id: {type: GraphQLString},
    user: {
      type: UserType, //need to declare the usertype
      args: {
        id: {
          type: GraphQLString
        }
      }
      resolve: (post) => //pass the object that we received from the API call.
        fetch( `${BASE_URL}/users/${post.userId}` ) //API call for users/1 say.
          .then(res => res.json())
    }

  })
}); 
```

*   我们现在需要声明我们的`User`类型，因为在我们的`Post`中已经有了

```
const UserType = new GraphQLObjectType({
  name: 'User',
  description: '...',
  fields: () => ({
    id: { type: GraphQLString },
    name: {type: GraphQLString},
    email: {type: GraphQLString}
  })
}); 
```

1.  总结一下，我们的`schema.js`应该是这样的

```
import {
  GraphQLList,
  GraphQLNonNull,
  GraphQLObjectType,
  GraphQLSchema,
  GraphQLString
} from 'graphql'

import fetch from 'node-fetch';

const BASE_URL = 'https://jsonplaceholder.typicode.com';

function getPostById(id) {
  console.log(id);
  return fetch(`${BASE_URL}/posts/${id}`,{
    headers:{
      'Content-Type': 'application/json'
    }
  }) //API call for posts/1 say.
    .then(res => res.json())
    .then(data => { 
      console.log(data);
      return data;
    });
}

function getAllPosts() {
  return fetch(`${BASE_URL}/posts`) //API call for posts/1 say.
    .then(res => res.json())
}

function getUser(id) {
  return fetch(`${BASE_URL}/users/${id}`) //API call for posts/1 say.
    .then(res => res.json())
}

const UserType = new GraphQLObjectType({
  name: 'User',
  description: '...',
  fields: () => ({
    id: { type: GraphQLString },
    name: { type: GraphQLString },
    email: { type: GraphQLString }
  })
});

const PostType = new GraphQLObjectType({
  name: 'Post',
  description: '...',
  fields: () => ({
    title: { type: GraphQLString },
    body: { type: GraphQLString },
    id: { type: GraphQLString },
    user: {
      type: UserType, //need to declare the usertype
      resolve: (post) => getUser(post.userId)
    }
  })
});

const QueryType = new GraphQLObjectType({
  name: 'Query',
  description: '...',
  fields: () => ({
    post: {
      type: PostType, //needs to be declared
      args: {
        id: { type: GraphQLString }
      },
      resolve: (root, args) => getPostById(args.id),
    },
    posts: {
      type: new GraphQLNonNull(new GraphQLList(new GraphQLNonNull(PostType))), //array of posts
      resolve: () => getAllPosts()
    },
  })
});

module.exports = new GraphQLSchema({ //essentially our schema string from previous example
  query: QueryType,
}); 
```

如果有任何疑问/错误，请告诉我们。

1.  现在让我们一起玩 graphql 吧。

-列出所有文章的标题

[![post titles](img/9e9a5383b4464d571446c39009eee894.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NYI5Qpnk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./posts.png)

-列出`id:1`的帖子详细信息

[![post](img/e775b18f7ee19a185be094f624290067.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wA--G0uH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./post.png)

那是一个服务器端包装器，你可以在这里检查服务器端包装器的 repo [。如果你想看一个关于加载器的更全面的例子，你可以查看服务器端包装器的](https://github.com/nishantrpai/graphql-server-wrapper) [GraphQL 官方文档](https://graphql.org/blog/rest-api-graphql-wrapper/#a-server-side-rest-wrapper)。

[![its done](img/43c5bcacccc466c356a9b2ae3ca9cca7.png)](https://i.giphy.com/media/oxdVsTscIKUwg/giphy.gif)

希望您对 GraphQL 的服务器端包装器的工作原理有所了解。

1.  **客户端包装器**

[![server wrapper](img/de5dd1c4506d90a3575f21d275d6ef67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8JZ3S7qA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./c_w.png)

*分享本博客更多这样的 OC 插图*😅

如前所述，我们将在此讨论如何与 React.js 集成:

为此，我们使用了[阿波罗客户端](https://github.com/apollographql/apollo-client)和[创建反应应用](https://github.com/facebook/create-react-app)。

*注意:代码的哪一部分去了哪里*可能会让人感到困惑😅*。别害怕，我们会掩护你的...我们在这里为客户端包装添加了一个回购，您可以参考使用。*

[![Lets go](img/02383c167027b1ed8ed0eac27daf3e5b.png)](https://i.giphy.com/media/3o7TKUM3IgJBX2as9O/giphy.gif)

1.  为此，我们将使用`create-react-app`和`graphql-client-wrapper`。

2.  在继续之前，我们需要在项目中设置一些库。

只需在本地运行这些命令，它们应该会安装所需的库:

*   `npm install --save apollo-client`

*   `npm install --save apollo-cache-inmemory`

*   `npm install --save apollo-link-rest apollo-link graphql graphql-anywhere qs`

*   `npm install --save graphql-tag`

*您也可以执行“NPM I-save *包名* ` *

1.  现在我们需要设置我们的客户端来与 REST API 交互

```
//setup the REST API
import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';
import { RestLink } from 'apollo-link-rest';
import gql from 'graphql-tag';
const restLink = new RestLink({ uri: "https://jsonplaceholder.typicode.com/" });

const client = new ApolloClient({
  link: restLink,
  cache: new InMemoryCache(),
}); 
```

1.  我们需要在`App.js`中设置两条路线

-我们所有帖子的路线

-用`:id`
为单个帖子选择路由器

```
 <Router>
    <Route path="/" exact component={Posts} />
    <Route path="/posts/:id" component={Post} />
  </Router> 
```

1.  我们需要制作一个`query`来让`Posts`通过我们的`client`

顺便说一下，我们之前忘记提到`query`是一个用于读取和获取值的 GraphQL 操作，这就是为什么我们一直使用`query`命令来获取数据。

```
export function getAllPosts() {
  return new Promise(function (resolve, reject) {
    const postsQuery = gql`
      query postsQuery {
        posts @rest(type: "[Post]", path: "/posts") {
          id
          title
        }
      }
    `
    client.query({ query: postsQuery })
      .then(response => {
        resolve(response.data);
      })
      .catch(err => {
        reject([]);
      })
  });
} 
```

1.  类似地，我们需要查询为`id`获取适当的`Post`

```
export function getPost(id) {
  return new Promise(function (resolve, reject) {
    const postsQuery = gql`
      query postsQuery {
        post @rest(type: "[Post]", path: "/posts/${id}") {
          id
          userId @export(as: "id")
          title
          body
          user @rest(type: "User", path: "/users/{exportVariables.id}") { 
            # getting the user data from userId field.
            name
          }
        }
      }
    `
    client.query({ query: postsQuery })
      .then(response => {
        resolve(response.data);
      })
      .catch(err => {
        reject([]);
      })
  });
} 
```

1.  现在，我们的请求已经设置好了，我们需要定义我们的`containers`来显示我们所有的帖子和我们之前在`Routes`中声明的单个帖子。

```
import React from 'react';
import { getAllPosts } from '../../services';

class Posts extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      posts: []
    }
  }

  componentDidMount() {
    getAllPosts().then(data => { this.setState({ posts: data.posts }));
  }

  render() {
    const { posts } = this.state;
    console.log(posts, posts.length > 0);
    return (
      <div>
        {posts.length > 0 && posts.map(post => <p><a href={`/posts/${post.id}`}>{post.title}</a></p>)}
      </div>
    );
  }
}
export default Posts; 
```

我们对`componentDidMount`进行 API 调用，并根据接收到的数据设置`posts`的状态。我们构建的 GraphQL 层被用作服务，因此容器不知道下面是什么。如果使用 redux，您可以将服务与动作集成在一起。

1.  类似地，对于`Post`，我们做了类似的事情

```
import React from 'react';
import { getPost } from '../../services';

class Post extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      post: {
        id: '',
        title: 'Loading...',
        body: '',
        user: {
          name: ''
        }
      }
    }
  }
  componentDidMount() {
    getPost(this.props.match.params.id).then(data => this.setState({ post: data.post }));
  }
  render() {
    return (
      <div style={{maxWidth: '500px', margin: 'auto'}}>
        <h1>{this.state.post.title}</h1>
        <h3>{this.state.post.user.name}</h3>
        <p>{this.state.post.body}</p>
      </div> 
    );
  }
}
export default Post; 
```

类似于我们对`Posts`的方法，我们对`Post`也这样做了。

这是非常基本的设置。我们没有添加很多 CSS，因为我们的重点是将 GraphQL 作为服务集成到我们的容器中。

使用客户端包装器的一个主要缺点是，**负载保持不变**。而使用服务器端包装器，客户端只接收所需的量(这对 web 应用程序的性能很有帮助)。

## 结论

*   GraphQL 可以作为 REST API 上的客户端/服务器端包装器

*   GraphQL 允许您确定响应的结构，而不会干扰整个后端。

*   如果您计划迁移您现有的 API，您可以使用像`apollo-client`和`express-graphql`这样的库😊根据你的包装方式。

*   这里是我们的[服务器端包装器](https://github.com/nishantrpai/graphql-server-wrapper)，这里是我们的[客户端包装器](https://github.com/nishantrpai/graphql-client-wrapper)。

此外，让我们知道你对这个博客🖖.的想法

[![party](img/206348fa3db1bf1df1885496e1f21879.png)](https://i.giphy.com/media/l0MYt5jPR6QX5pnqM/giphy.gif)