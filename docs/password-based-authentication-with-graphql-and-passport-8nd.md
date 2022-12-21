# 使用 GraphQL 和 Passport 进行基于密码的认证

> 原文：<https://dev.to/jkettmann/password-based-authentication-with-graphql-and-passport-8nd>

使用 Passport.js 的标准方法是在服务器上为注册和登录设置单独的端点。前端将数据从相应的表单发送到这些专用端点。认证成功后，用户被重定向到某个 URL。

这感觉不太像 GraphQL。您更希望有一个注册和一个登录变异来处理完整的身份验证流程，从向服务器发送凭证、对用户进行身份验证，到之后更新客户端缓存。这就是我们要做的。

本文中的代码基于我们在[上一篇介绍性文章](https://jkettmann.com/authentication-and-authorization-with-graphql-and-passport/)中实现的 GraphQL API 服务器。你不需要阅读这篇文章。但是如果你理解有困难，或者想要更大的图片，你可以看看那里。

首先，服务器已经支持获取登录用户数据的查询和注销的变异。它还被设置为使用[护照](https://github.com/jaredhanson/passport)和[快速通道](https://github.com/expressjs/session)。您可以在 GitHub 上找到[代码，并在本文中添加代码片段。](https://github.com/jkettmann/authentication-with-graphql-passport-and-react-starter)

## 为 Passport 添加 GraphQL 本地策略

Passport 通常需要单独的端点进行身份验证。因此，在 GraphQL 解析器中处理注册和登录不支持开箱即用。为此，我创建了一个名为 [graphql-passport](https://github.com/jkettmann/graphql-passport) 的小型 npm 模块。这允许我们从 GraphQL 上下文中访问 Passport 的功能，并为我们提供了一种策略来根据本地数据库使用用户的凭证对用户进行身份验证。让我们将它添加到我们的依赖项中。

```
npm install graphql-passport 
```

首先，我们需要在`api/index.js`中用这个策略初始化 Passport。

```
import express from 'express';
import passport from 'passport';
import { GraphQLLocalStrategy } from 'graphql-passport';
import User from './User';

passport.use(
  new GraphQLLocalStrategy((email, password, done) => {
    const users = User.getUsers();
    const matchingUser = users.find(user => email === user.email && password === user.password);
    const error = matchingUser ? null : new Error('no matching user');
    done(error, matchingUser);
  }),
);

const app = express();
app.use(passport.initialize());

... 
```

这里我们不使用真实的数据库，以便将重点放在用户认证上。因此，我们使用一个[模拟数据库模型](https://github.com/jkettmann/authentication-with-graphql-passport-and-react-starter/blob/master/api/User.js)来查找所提供的电子邮件和密码的匹配。如果我们找到匹配，我们将用户传递给`done`回调。否则，我们创建一个错误并将其传递给`done`。

## 准备 GraphQL 上下文

在我们继续并实现变异之前，我们需要准备 GraphQL 上下文，以使解析器可以访问某些 Passport 函数。为此，我们可以使用 graphql-passport 的`buildContext`函数。`api/index.js`内部的 Apollo 服务器的初始化现在看起来像这样。

```
import { GraphQLLocalStrategy, buildContext } from 'graphql-passport';

...

const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req, res }) => buildContext({ req, res }),
});

... 
```

`buildContext`基本上是从请求中复制一些与 Passport 相关的字段，比如它的`authenticate`和`login`函数到上下文中，使它们可以在解析器中使用。如果你感兴趣，你可以在这里找到[源代码。](https://github.com/jkettmann/graphql-passport/blob/master/src/buildContext.js)

## 实现登录突变

首先，让我们将登录变异添加到 GraphQL 类型定义中。我们需要用户的`email`和`password`作为输入变量，并在成功时返回匹配的用户对象。在`api/typeDefs.js`中增加以下几行。

```
const typeDefs = gql`
  ...

  type AuthPayload {
    user: User
  }

  type Mutation {
    login(email: String!, password: String!): AuthPayload
    ...
  }
`; 
```

现在我们需要实现相应的解析器。打开`api/resolvers.js`并添加以下几行。

```
const resolvers = {
  ...
  Mutation: {
    login: async (parent, { email, password }, context) => {
      const { user } = await context.authenticate('graphql-local', { email, password });
      context.login(user);
      return { user }
    },
    ...
  }
};

export default resolvers; 
```

首先，我们在上下文中调用`authenticate`函数。我们向它传递我们使用的策略的名称( *graphql-local* )和我们可以从变异变量中读取的凭证。为了创建持久的用户会话，Passport 要求我们在认证后调用`login`函数。

现在我们可以运行`npm start`来启动 GraphQL 服务器。打开浏览器，指向 [localhost:4000/graphql](http://localhost:4000/graphql) 。当您运行下面的登录变异时，它应该返回在`User.js`中定义的相应用户对象。

```
mutation  {  login(email:  "maurice@moss.com",  password:  "abcdefg")  {  user  {  id  firstName  lastName  email  }  }  } 
```

## 实现签约突变

最后，让我们也实现注册变异。我们再次从类型定义开始。这与登录变异非常相似。我们只期望名字和姓氏作为附加变量。

```
const typeDefs = gql`
  ...

  type Mutation {
    signup(firstName: String!, lastName: String!, email: String!, password: String!): AuthPayload
    ...
  }
`; 
```

因为我们需要将新用户添加到现有用户的列表中，所以我们需要从解析器访问用户模型。因此，我们需要将其添加到 GraphQL 上下文中。我们可以通过传递用户模型在`api/index.js`中构建上下文来实现这一点。

```
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req, res }) => buildContext({ req, res, User }),
}); 
```

`buildContext`会将您传递给它的所有附加字段添加到上下文中。

现在我们可以前往`resolvers.js`。首先，我们需要检查具有所提供的电子邮件的用户是否已经存在。如果没有，我们添加用户。否则，我们抛出一个错误。我们还希望用户在注册后直接登录。为了创建一个持久会话并设置相应的 cookie，我们还需要在这里调用`login`函数。

```
import uuid from 'uuid/v4';

const resolvers = {
  ...
  Mutation: {
    signup: (parent, { firstName, lastName, email, password }, context) => {
      const existingUsers = context.User.getUsers();
      const userWithEmailAlreadyExists = !!existingUsers.find(user => user.email === email);

      if (userWithEmailAlreadyExists) {
        throw new Error('User with email already exists');
      }

      const newUser = {
        id: uuid(),
        firstName,
        lastName,
        email,
        password,
      };

      context.User.addUser(newUser);

      context.login(newUser);

      return { user: newUser };
    },
    ...
  },
};

export default resolvers; 
```

当您使用`npm start`运行服务器并在 [localhost:4000/graphql](http://localhost:4000/graphql) 打开浏览器时，您应该能够执行下面的注册变异。

```
mutation  {  signup(  firstName:  "Jen",  lastName:  "Barber",  email:  "jen@barber.com",  password:  "qwerty"  )  {  user  {  id  firstName  lastName  email  }  }  } 
```

您可以使用您在注册突变中使用的电子邮件和密码运行登录突变，并查看新创建的用户。请注意，一旦您重启服务器，新用户将会消失(或者实际上在`api`目录中保存一个文件，因为我们使用 nodemon)。

当您在登录或注册后发送查询时，下面的`currentUser`查询应该返回相同的数据。

```
{  currentUser  {  id  firstName  lastName  email  }  } 
```

你可以在 GitHub 上找到完整的代码[。](https://github.com/jkettmann/password-based-authentication-with-graphql-and-passport)

我希望这篇文章对你有帮助。如果你喜欢，我很乐意在我的简讯上看到你[。如果你对 GraphQL 和 Passport.js 的更多认证方式感兴趣，看看这篇文章，学习](https://jkettmann.com/subscribe/)[如何使用脸书](https://jkettmann.com/facebook-login-with-graphql-and-passport/)实现社交登录。