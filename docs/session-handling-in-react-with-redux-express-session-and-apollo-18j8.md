# React with Redux、express-session 和 Apollo 中的会话处理

> 原文：<https://dev.to/tmns/session-handling-in-react-with-redux-express-session-and-apollo-18j8>

### [T1】简介](#intro)

下面是一个简短的文档，介绍了我是如何设法用 React、Redux、`express-session`和 Apollo 设置会话处理的。

一些必要的免责声明:

*   它并不意味着被用作一个完整的，从开始到结束的教程，它通常假设对上述所有技术有一个基本的理解。
*   但是，如果您正在走类似的道路，它绝对可以作为一个起点或参考，特别是结合您自己的研究和附带的代码库。
*   我不是这些技术的专家。这就是我如何解决我所面临的问题。如果任何人有建议/更好的处理方法，请告诉我！

### 上下文

我一直在构建一个间隔重复学习应用程序，你可以在这里查看[的 github repo。我决定分三个阶段构建应用程序。第一波，我简单地构建了一个本地 CLI 版本，功能齐全，可以在](https://github.com/tmns/never-forget-app)[这里](https://github.com/tmns/never-forget-cli)构建/安装。第二波，我构建了一个[后端 API](https://github.com/tmns/never-forget-server) 的草稿(最终的结果是【尽管当时我认为它已经完成了】)。正是在这一浪潮中，我自然而然地遇到了认证和授权的关键问题。

这是我第一个完全独立开发的全栈应用。在过去做教程时，他们倾向于利用 JSON Web 令牌(JWT)进行认证和会话处理。然而，经过一点点研究，从安全角度来看，将 JWT 用于此目的似乎是[而不是](https://gist.github.com/samsch/0d1f3d3b4745d778f78b230cf6061452) [有争议的](https://gist.github.com/joepie91/cf5fd6481a31477b12dc33af453f9a1d)。当然，我本质上是在做一个抽认卡应用，而不是银行应用，但是如果我在一个不稳定的基础上开发出像 AuthN 这样重要的东西，我过去的安全研究员不会让我心安理得。此外，用户研究的内容可以提供相当多的关于他们是谁的信息，因此确实存在隐私问题。

因此，我决定用经过试验和验证的`express-session`进行会话处理，用`connect-mongodb-session`作为会话存储。但是，当在客户端将它与 Apollo 捆绑在一起时，这将被证明是有点棘手的。

### 服务器端

在后端，实现会话处理相对简单。首先，我们在`server.js`中导入相关包(注意，我在`import`语句中使用 transpiling。此外，我当然忽略了不相关的代码。完整代码见 github repo):

```
import express from "express";
import { ApolloServer } from "apollo-server-express";
import session from "express-session";

var MongoDBStore = require("connect-mongodb-session")(session); 
```

然后，我们设置 Apollo 和会话处理:

```
[...]
var server = new ApolloServer({
  typeDefs: [rootSchema, ...schemaTypes],
  resolvers: merge({}, user, deck, card),
  context(req) {
    return { ...req.req };
  }
});

var app = express();
[...]
var store = new MongoDBStore({
  uri: config.DB_URI,
  collection: "sessions"
});

store.on("error", function(error) {
  console.log(error);
});

app.use(
  session({
    name: config.SESS_NAME,
    secret: config.SESS_SECRET,
    resave: true,
    saveUninitialized: false,
    cookie: {
      secure: process.env.NODE_ENV == "production",
      maxAge: config.SESS_LIFETIME
    },
    store: store
  })
);
[...]
var corsOptions = {
  origin: ["http://localhost:3000", serverUrl],
  credentials: true
};

app.use(cors(corsOptions));

server.applyMiddleware({ app, cors: false });
[...] 
```

注意，我们必须为 Apollo 服务器设置`corsOptions`中的`credentials: true`,以便 cookie 数据与 graphql 请求一起发送。此外，由于我们在`corsOptions`中手动设置了这个选项，我们也必须在调用`server.applyMiddleware`时手动禁用`cors`选项；否则，我们的价值观将被取代。感谢 Ryan Doyle 解决了这个难题。

还要注意，我们用`req.req`构建了一个`context`对象。这很重要，因为我们将在那里存储用户对象，并在所有 authN 和 authZ 检查中使用它。

例如，我们的`login`和`logout`解析器(`types/user/user.resolver.js`)可能如下所示:

```
async function login(_, args, ctx) {
  if (isAuthenticated(ctx.session)) {
    throw new ForbiddenError("User already authenticated");
  }
  try {
    return await loginUser(
      args.input.username,
      args.input.password,
      ctx.session
    );
  } catch (err) {
    throw err;
  }
}

async function logout(_, args, ctx) {
  if (!isAuthenticated(ctx.session)) {
    throw new AuthenticationError("User not authenticated");
  }
  return await logoutUser(ctx);
} 
```

...其中`isAuthenticated`、`loginUser`、`logoutUser` ( `utils/auth.js`)定义为:

```
function isAuthenticated(session) {
  return session.user != undefined;
}

async function loginUser(username, password, session) {
  if (isValidUsername && isValidPassword) {
    var user = await User.findOne({ username });
    if (user != null && (await user.checkPassword(password))) {
      session.user = {
        _id: user._id,
        username: user.username
      };
      return session.user;
    }
  }
  throw new UserInputError("Invalid username or password.");
}

async function logoutUser(ctx) {
  var loggedOutUser = ctx.session.user;
  await ctx.session.destroy();
  ctx.res.clearCookie(SESS_NAME);
  return loggedOutUser;
} 
```

注意我们如何简单地检查`user`对象是否存在于 authN 检查的上下文(`ctx`)中？只要我们确保正确地添加和删除用户对象(在`session.destroy()`这样的`express-session`内置工具的帮助下)，我们就可以放心地睡觉，因为简单的 authN 检查就足够了。

### 客户端

好吧，所以我们可以在后端通过失眠或邮递员登录和注销一整天，但是我们如何将它绑定到我们的 React 前端呢？虽然现在似乎每个人都认为最直接的方法是使用 React 的`useContext` API，但我发现的最可行的方法(也就是说，我实际上理解并能够在没有大麻烦的情况下工作)是使用 good ol' Redux。

我在应用程序的登录页面使用 Formik 所以，`onSubmit`看起来像这样(`client/src/components/auth/SignIn.js`):

```
[...]
<Fragment>
  <Formik
    initialValues={initialState}
    validationSchema={validationSchema}
    onSubmit={async (values, actions) => {
      const variables = {
        input: {
          username: values.username,
          password: values.password
        }
      };
      try {
        await signIn(variables);
        actions.setSubmitting(false);
        history.push("/dashboard");
      } catch (err) {
        console.log(err);
        actions.setSubmitting(false);
        actions.setStatus({ msg: "Invalid username or password." });
      }
    }}
  >
[...] 
```

注意我们是如何调用`signIn`函数的，在我们的例子中，它是一个 Redux 动作(`client/src/actions/session.js`):

```
import * as apiUtil from '../util/session';

export const RECEIVE_CURRENT_USER = 'RECEIVE_CURRENT_USER';
export const LOGOUT_CURRENT_USER = 'LOGOUT_CURRENT_USER';

const receiveCurrentUser = user => ({
  type: RECEIVE_CURRENT_USER,
  user
})

const logoutCurrentUser = () => ({
  type: LOGOUT_CURRENT_USER
})

export const signIn = variables => async dispatch => {
  try {
    var data = await apiUtil.signIn(variables);
    return dispatch(receiveCurrentUser(data));
  } catch(err) {
    throw err;
  }
}

export const signOut = () => async dispatch => {
  try {
    await apiUtil.signOut();
    return dispatch(logoutCurrentUser());
  } catch(err) {
    throw err;
  }
} 
```

当然，相关的缩减器看起来像(`client/src/reducers/session.js`):

```
import { RECEIVE_CURRENT_USER, LOGOUT_CURRENT_USER } from "../actions/session";

const _nullSession = {
  username: null,
  userId: null
};

export default (state = _nullSession, { type, user }) => {
  Object.freeze(state);
  switch (type) {
    case RECEIVE_CURRENT_USER:
      return user;
    case LOGOUT_CURRENT_USER:
      return _nullSession;
    default:
      return state;
  }
}; 
```

我们已经定义了我们的 reducers 和 actions，但是我们如何让 Apollo 客户端调用与我们的 graphql 服务器端解析器进行实际的交互呢？您会注意到在我们的操作中我们引用了`util/session`，让我们来看看:

```
import { gql } from "apollo-boost";

// this is placed in its own module in the actual codebase
const client = new ApolloClient({
  uri: "http://localhost:4000/graphql",
  credentials: "include"
});

const signInMutation = gql`
  mutation signin($input: LoginUserInput!) {
    login(input: $input) {
      username
      _id
    }
  }
`;

const signOutMutation = gql`
  mutation logout {
    logout {
      username
      _id
    }
  }
`;

async function signIn(variables) {
  var data = await client.mutate({ mutation: signInMutation, variables });
  return {
    username: data.data.login.username,
    userId: data.data.login._id
  }
}

async function signOut() {
  return await client.mutate({ mutation: signOutMutation })
} 
```

在这里，我们手动创建我们的 Apollo 客户机，并写出相关的 graphql 变化。最后，我们在调用`client.mutate`时使用它们。这是我发现的执行此类操作的最直接的方式，最终我在几乎所有的 Apollo 客户机/服务器交互中都使用了类似的模式。我特别想从阿波罗专家那里了解是否有更好的处理方式。

最后一个小问题是确保你想要的受保护的反应路线实际上是受保护的！这可以通过下面这样的方法来实现:

```
import React from "react";
import { Route, Redirect } from "react-router-dom";
import { connect } from "react-redux";

const mapStateToProps = ({ session }) => ({
  loggedIn: Boolean(session.userId)
});

const ProtectedRoute = ({ loggedIn, component: Component, ...rest }) => (
  <Route
    {...rest}
    render={props =>
      loggedIn ? <Component {...props} /> : <Redirect to="/signin" />
    }
  />
);

export default connect(mapStateToProps)(ProtectedRoute); 
```

...最后在`App.js`:

```
[...]
function App() {
  return (
    <BrowserRouter>
        <div>
            <Route exact path="/" component={Landing}
[...]
                <ProtectedRoute exact path="/dashboard" component={Dashboard} />         </div>
    </BrowserRouter>
  );
}

export default App; 
```

就是这样！现在，我们已经在整个堆栈中实现了身份验证和会话处理，所有部分都协调工作。

### 结论

随着 JWT 在会话处理方面的使用越来越流行，很明显缺少将`express-session`与 React 和 Apollo 一起使用的文档。此外，虽然现在许多博客都提倡使用`useContext`进行这种应用范围的状态跟踪，但在我看来，使用 Redux 似乎更合适、更简单。为了与 Apollo 服务器进行交互，我选择将相关的 authN 逻辑抽象到它自己的模块中，并进行手动客户端变异查询。

这是一个很难拼凑起来的谜，但最终它似乎运行得相当好。我鼓励你通过克隆应用程序的 [repo](https://github.com/tmns/never-forget-app) 并自己构建/运行它来试验它！

当然任何建议都是受欢迎的！