# [过时]我如何用 Next.js 和 MongoDB 构建一个成熟的应用程序第 1 部分:用户认证

> 原文：<https://dev.to/hoangvvo/how-i-build-a-full-fledged-app-with-next-js-and-mongodb-part-1-user-authentication-3io9>

## 这并不反映最近改写的`nextjs-mongodb-app`。看看最新版本的。

**更新**:为了兼容性，我把 argon2 换成了 bcryptjs。

上个月，我一直在努力为我的下一个项目实现一个认证系统。我发现它是一个用代码解决社区问题的组织的一部分。

该项目是一个促进良好行为的**在线**游戏，所以它需要有一个用户登录的方法。

我正在使用 React 框架 [Next.js](https://nextjs.org/) 和 [MongoDB](https://www.mongodb.com) 作为我的数据库。

我曾经有一个 Express 后端作为 Next.js 定制服务器，在那里我(懒惰地)使用 PassportJS 来处理认证。我意识到这样做并不能很好地控制我的 API。因此，我决定使用我的认证系统。

一个可能的反驳观点是，我不应该重新发明轮子。尽管如此，我想要探索和挑战，所以我选择了这条路。

我在网上找到的大多数解决方案要么依赖于第三方服务(谷歌、脸书、身份即服务)，要么半生不熟，没有考虑到现实生活中的应用。因此，我决定做我自己的。

下面是 Github 库和这个项目的演示。

[Github 回购](https://github.com/hoangvvo/nextjs-mongodb-app)

[演示](https://nextjs-mongodb-app.hoangvvo.now.sh/)(固定)

## 关于`nextjs-mongodb-app`项目

> nextjs-mongodb-app 是一个用 Next 构建的成熟应用。JS 和 MongoDB。互联网上的大多数教程要么半生不熟，要么不适合生产。这个项目旨在解决这个问题。
> 
> 这个项目甚至更进一步，试图集成现实生活中应用程序的顶级功能，使其成为一个成熟的应用程序。

欲了解更多信息，请访问 [Github repo](https://github.com/hoangvvo/nextjs-mongodb-app) 。

## 入门

典型的教程会告诉你做什么，但是我相信你已经是一个有经验的开发者了。

我将从搭建我的应用程序开始。

### 环境变量

该项目从`process.env`中检索变量。你需要实施你的策略。一些可能的解决方案是:

*   [https://github . com/zeit/next . js/tree/canary/examples/with-dotenv](https://github.com/zeit/next.js/tree/canary/examples/with-dotenv)
*   在服务提供者中设置环境变量(例如 now.sh env 或 heroku config var)

本项目所需的环境变量包括:

*   process.env. **MONGODB_URI**

### 请求库

这个项目将需要有一个请求库来对 API 进行请求。请随意选择您自己的。以下是一些建议:

*   [axios](https://www.npmjs.com/package/axios) (我的选择)
*   [同构-解耦](https://www.npmjs.com/package/isomorphic-unfetch)
*   [请求](https://www.npmjs.com/package/request)

### 验证库

我使用 [validator](https://www.npmjs.com/package/validator) 进行验证，但是你可以随意使用你的库或者写你的支票。

### 密码哈希库

密码必须经过哈希处理。句号。有不同的图书馆:

*   [bcrypt](https://www.npmjs.com/package/bcrypt) (最常见)
*   scrypt
*   [argon2](https://www.npmjs.com/package/argon2) (我的选择)

请不要使用 MD5、SHA1 或 SHA256！

### 獴...我是说 MongoDB

*猫鼬*不是*猫鼬*。这是一条完全不同的道路。我看到很多教程交替使用 Mongoose 和 MongoDB，这是非常错误的。

*mongose*是一个*对象数据建模*库。这通过在数据库中定义模式来确保数据的持久性和完整性，在我看来，这破坏了 **NoSQL** 的目的。

*NoSQL* 被设计成掉落验证和建模(这被称为[酸](https://en.wikipedia.org/wiki/ACID))。这意味着任何类型的数据都可以接受，并且未来对结构的修改也很灵活。另一方面，*mongose*将要求你对所有事情都有一个模式。如果我将我的`Age`字段定义为`Number`，试图保存一个`String`到该字段将会产生一个错误。

请注意，使用 Mongoose 会导致性能的[显著下降，因为每次读写都必须验证数据(这证明了删除`ACID`)。](https://medium.com/@bugwheels94/performance-difference-in-mongoose-vs-mongodb-60be831c69ad)

如果你打算使用*猫鼬*，这里是我为`User` :
准备的模式

```
const UserSchema = new mongoose.Schema({
  email: {
    type: String,
    trim: true,
    minlength: 1,
    unique: true,
    index: true,
  },
  emailVerified: {
    type: Boolean,
  },
  password: {
    type: String,
    minlength: 6,
  },
  name: {
    type: String,
  },
 });

 export default mongoose.models.User || mongoose.model('User', UserSchema); 
```

Enter fullscreen mode Exit fullscreen mode

## 构建成熟的认证。

### 响应模式

> 测量两次，切割一次

我怎么强调都不为过。我希望在我的 API 响应中看到持久性。因此，我提出了这个模式——每个 API 的标准响应:

```
{  "status":  "ok / error",  "message":  "a user-readable message",  "data":  "<payload object>"  } 
```

Enter fullscreen mode Exit fullscreen mode

请随意享用你自己的。要获得一些灵感，请参见这个 [Stackoverflow 讨论](https://stackoverflow.com/questions/12806386/standard-json-api-response-format)。

如果我没有这个计划，我可能会遇到几个问题。想象一下我的一个端点的响应:

```
{  "success":  true,  "msg":  "successful stuff",  } 
```

Enter fullscreen mode Exit fullscreen mode

...而另一个人的回答是:

```
{  "error":  false,  "text":  "ok",  } 
```

Enter fullscreen mode Exit fullscreen mode

我将不得不写两个不同的检查，并尝试使用两个不同的字段显示消息:

```
if (response.success === true || response.error === false) {
  alert(response.msg || response.text);
} 
```

Enter fullscreen mode Exit fullscreen mode

它会很快变坏，尤其是当端点的数量增加并且没有文档记录时。

### 中间件

如果你有`ExpressJS`的背景，你可能对术语*中间件*很熟悉。

在 Next.js 中使用中间件的方法是将原来的`handler`(`(req, res)`的 API Route 函数)an 作为参数，返回一个新的具有附加功能的`handler`。

#### 数据库中间件

我们将需要一个处理数据库连接的中间件，因为我们不想在每条路线上都调用数据库。

创建`middlewares/withDatabase.js` :

```
import { MongoClient } from 'mongodb';

const client = new MongoClient(process.env.MONGODB_URI, { useNewUrlParser: true });

const withDatabase = handler => (req, res) => {
  if (!client.isConnected()) {
    return client.connect().then(() => {
      req.db = client.db('nextjsmongodbapp');
      return handler(req, res);
    });
  }
  req.db = client.db('nextjsmongodbapp');
  return handler(req, res);
};
export default withDatabase; 
```

Enter fullscreen mode Exit fullscreen mode

如果客户端没有连接，我首先会尝试连接。

然后，我将数据库连接到`req.db`，将客户端连接到`req.mongoClient`。客户端可以在我们的会话中间件中重用。

不建议对您的 MongoDB URI 或任何其他安全变量进行硬编码。我是通过`process.env`得到的。

#### 会话中间件

会话是我们项目中的关键要素之一。在 ExpressJS 中，我们可以使用`express-session`。在 Next.js 中，要使用的会话中间件是我的[下一个会话](https://www.npmjs.com/package/next-session)。你可以继续安装它或者使用/制作你的中间件。

```
npm install next-session 
```

Enter fullscreen mode Exit fullscreen mode

注意:目前，`next-session`没有任何本地会话存储，但通过将`storePromisify`设置为`true`，它与`express-session`会话存储兼容。

默认商店是`MemoryStore`，它还没有准备好生产。现在，我们可以使用 [connect-mongo](https://www.npmjs.com/package/connect-mongo) ,因为我们已经在使用 MongoDB:

创建`middlewares/withSession.js` :

```
import session from 'next-session';
import connectMongo from 'connect-mongo';

const MongoStore = connectMongo(session);

const withSession = handler => session.withSession(handler, {
  store: new MongoStore({ url: process.env.MONGODB_URI }),
});

export default withSession; 
```

Enter fullscreen mode Exit fullscreen mode

`MongoStore`也要求`session`。我们的`session`中间件是`next-session`。

#### 全局中间件

这是我的中间件方法，我将引用它来自`next-session`。

> 实际上，您不希望在每个函数中都将 session()包装在 handler 周围。您可能会遇到一个会话()的配置与其他会话不同的情况。一个解决方案是创建一个全局中间件。

只需创建`middlewares/withMiddleware.js`并包含我们的其他中间件:

```
import withDatabase from './withDatabase';
import withSession from './withSession';

const middleware = handler => withDatabase(withSession(handler));

export default middleware; 
```

Enter fullscreen mode Exit fullscreen mode

要知道`withDatabase(withSession(handler))`中的顺序是物质。稍后我们将添加一个名为`withAuthentication`的中间件，它将利用我们的数据库。因此，我们将把`withAuthentication`放在`withDatabase`内，把`withSession`放在里面，否则数据库将不会准备好，会话也不会存在。

### `components/layout`:布局

这部分是**可选的**。这是您可以包含 Header.jsx 的地方，只要您用`<Layout>`将页面换行，它就会出现在每一页上。

我准备用 Next.js `styled-jsx`添加一些样式。

```
import React from 'react';

export default ({ children }) => (
  <>
    <style jsx global>
      {`
        * {
          box-sizing: border-box;
        }
        body {
          color: #4a4a4a;
          background-color: #f8f8f8;
        }
        input {
          width: 100%;
          margin-top: 1rem;
          padding: 1rem;
          border: none;
          background-color: rgba(0, 0, 0, 0.05);
        }
        button {
          color: #ecf0f1;
          margin-top: 1rem;
          background: #009688;
          border: none;
          padding: 1rem;
        }
      `}

    </style>
    { children }
  </>
); 
```

Enter fullscreen mode Exit fullscreen mode

### 用户注册

让我们从用户注册开始，因为我们至少需要一个用户。

#### 构建注册 API

假设我们通过向`/api/users`发送一个`POST`请求来注册用户，请求中包含一个名字、一个电子邮件和一个密码。

`/api`中的一切都是 API 路线，[是 Next.js 9](https://www.hoangvvo.com/blog/explore-next-js-9-api-routes/) 的新增加。每个都必须导出一个带两个参数`req`和`res`的函数。以下是我们`users.js` :
的内容

```
import isEmail from 'validator/lib/isEmail';
import * as argon2 from 'argon2';
import withMiddleware from '../../middlewares/withMiddleware';

const handler = (req, res) => {
  if (req.method === 'POST') {
    const { email, name, password } = req.body;
    if (!isEmail(email)) {
      return res.send({
        status: 'error',
        message: 'The email you entered is invalid.',
      });
    }
    return req.db.collection('users').countDocuments({ email })
      .then((count) => {
        if (count) {
          return Promise.reject(Error('The email has already been used.'));
        }
        return argon2.hash(password);
      })
      .then(hashedPassword => req.db.collection('users').insertOne({
        email,
        password: hashedPassword,
        name,
      }))
      .then((user) => {
        req.session.userId = user.insertedId;
        res.status(201).send({
          status: 'ok',
          message: 'User signed up successfully',
        });
      })
      .catch(error => res.send({
        status: 'error',
        message: error.toString(),
      }));
  }
  return res.status(405).end();
};

export default withMiddleware(handler); 
```

Enter fullscreen mode Exit fullscreen mode

该函数验证电子邮件，散列密码，并将用户插入数据库。之后，我们将会话中的`userId`设置为新创建的对象的 id。

您可以看到，在继续之前，我首先检查方法是否为`POST`。如果不是，我返回状态码 [405 方法不允许](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/405)。

如果用户被创建，我将`req.session.userId`设置为创建的对象 id。我以后会探讨的。

还要注意，在每种错误情况下，我简单地返回一个带有错误对象的[拒绝](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)。拒绝将在`.catch()`被捕获，在那里我通过`.toString()`发送回一个带有错误消息的响应(错误是一个[错误对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)，因此需要被转换成一个字符串)。

#### `pages/signup.jsx`:报名页面

在`signup.jsx`中，我们会有以下内容:

```
import React, { useState } from 'react';
import axioswal from 'axioswal';
import Layout from '../components/layout';
import redirectTo from '../lib/redirectTo';

const SignupPage = () => {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (event) => {
    event.preventDefault();
    axioswal
      .post('/api/users', {
        name,
        email,
        password,
      })
      .then((data) => {
        if (data.status === 'ok') {
          redirectTo('/');
        }
      });
  };

  return (
    <Layout>
      <div style={{ margin: '4rem' }}>
        <h1>Sign up</h1>
        <form onSubmit={handleSubmit}>
          <div>
            <input
              type="text"
              placeholder="Your name"
              value={name}
              onChange={e => setName(e.target.value)}
            />
          </div>
          <div>
            <input
              type="email"
              placeholder="Email address"
              value={email}
              onChange={e => setEmail(e.target.value)}
            />
          </div>
          <div>
            <input
              type="password"
              placeholder="Create a password"
              value={password}
              onChange={e => setPassword(e.target.value)}
            />
          </div>
          <button type="submit">
            Sign up
          </button>
        </form>
      </div>
    </Layout>
  );
};

export default SignupPage; 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，让我们启动我们的开发服务器，导航到`/signup`并检查它。试着用一个有创意的想象中的电子邮件和一个糟糕的密码注册。

[![full-fledged app with Next.js and MongoDB: Signup 1](img/484feecce2bd1a990f588b770d3953c4.png)](//images.ctfassets.net/rgbpfpd0hrww/4I6G1MWjlpWuPDTJ5lfNNl/9e259a7e7b2bdd01cd0241b7b4cbb174/Signup_1.png)

此外，尝试:

*   用同样的电子邮件重新注册。
*   输入了一些无效的邮件。

它显示错误信息了吗？如果是的话，太好了！让我们回顾一下您刚刚完成的内容。

如果你不熟悉钩子，我使用 React [状态钩子](https://reactjs.org/docs/hooks-state.html) `useState`。在每个文本框中，我将其值设置为对应的`state`变量(`name`、`email`、`password`)，当它发生变化(`onChange`)时，我调用该方法(`setName`、`setEmail`、`setPassword`)并应用其新值(通过`e.target.value`)。

查看`onSubmit={handleSubmit}`，我们可以看到提交(通过按钮或 Enter)将调用函数`handleSubmit`，在这里我们将添加我们的登录流。`event.preventDefault()`阻止表单提交(到当前页面，导致页面重新呈现)。

在`preventDefault()`旁边`handleSubmit`要做的是向`/api/users`发出`POST`请求。之后，我还需要向用户显示一条**错误**或**成功**消息。

我使用我的`axioswal`，它发出 Axios 请求，处理响应，并根据响应显示一个 [sweetalert2](https://sweetalert2.github.io/) 对话框。

```
npm install axioswal 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在使用一个不同的请求库或者想要自己处理它，请随意。

另外，请注意，如果用户注册成功，我会重定向用户。我使用在`lib/redirectTo.js` :
定义的函数

```
import Router from 'next/router';

export default function redirectTo(destination, { res, status } = {}) {
  if (res) {
    res.writeHead(status || 302, { Location: destination });
    res.end();
  } else if (destination[0] === '/' && destination[1] !== '/') {
    Router.push(destination);
  } else {
    window.location = destination;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

摘自 Github 的一个片段，但我忘了在哪里了。如果你知道，告诉我:(

### 用户认证

现在我们有了一个用户。让我们尝试验证用户。我们实际上在用户注册时*已经*验证了他或她:

```
req.session.userId = user.insertedId; 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看如何在`/login`中做到这一点，在这里我们向`/api/authenticate`发出一个`POST`请求。

#### 构建认证 API

让我们创建`api/authenticate.js` :

```
import * as argon2 from 'argon2';
import withMiddleware from '../../middlewares/withMiddleware';

const handler = (req, res) => {
  if (req.method === 'POST') {
    const { email, password } = req.body;

    return req.db.collection('users').findOne({ email })
      .then((user) => {
        if (user) {
          return argon2.verify(user.password, password)
            .then((result) => {
              if (result) return Promise.resolve(user);
              return Promise.reject(Error('The password you entered is incorrect'));
            });
        }
        return Promise.reject(Error('The email does not exist'));
      })
      .then((user) => {
        req.session.userId = user._id;
        return res.send({
          status: 'ok',
          message: `Welcome back, ${user.name}!`,
        });
      })
      .catch(error => res.send({
        status: 'error',
        message: error.toString(),
      }));
  }
  return res.status(405).end();
};

export default withMiddleware(handler); 
```

Enter fullscreen mode Exit fullscreen mode

逻辑很简单，我们首先通过调用`findOne`查找电子邮件，给定`email`作为我们的查询。如果电子邮件不存在，我们拒绝并说“电子邮件不存在”。如果电子邮件确实存在，req.db.collection.findOne 会返回一个**文档**，我们称之为`user`。

然后我们尝试匹配密码。我调用`argon2.verify`(散列接收到的密码，并将其与散列后的密码进行比较)来查看我们从请求中获得的`password`是否与数据库中的匹配。如果不匹配，`argon2.verify()`返回 false。然后我们拒绝并说“您输入的密码不正确”。

然而，在现实中，我可能不希望有两个不同的回答，说是电子邮件还是密码不正确。它可能允许暴力破解。我们可以通过简单地将每个`Error()`对象都改为“您的电子邮件或密码不正确”来改变它。

如果匹配，我们将`userId`设置为类似于`Signup`的`req.session`，并返回一条带有用户名的“欢迎回来”的消息。

#### `pages/login.jsx`:登录页面

这是我们的代码`pages/login.jsx` :

```
import React, { useState } from 'react';
import axioswal from 'axioswal';
import Layout from '../components/layout';
import redirectTo from '../lib/redirectTo';

const LoginPage = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (event) => {
    event.preventDefault();
    axioswal
      .post('/api/authenticate', {
        email,
        password,
      })
      .then((data) => {
        if (data.status === 'ok') {
          redirectTo('/');
        }
      });
  };

  return (
    <Layout>
      <div style={{ margin: '4rem' }}>
        <h1>Log in</h1>
        <form onSubmit={handleSubmit}>
          <div>
            <input
              type="email"
              placeholder="Email address"
              value={email}
              onChange={e => setEmail(e.target.value)}
            />
          </div>
          <div>
            <input
              type="password"
              placeholder="Create a password"
              value={password}
              onChange={e => setPassword(e.target.value)}
            />
          </div>
          <button type="submit">
            Log in
          </button>
        </form>
      </div>
    </Layout>
  );
};

export default LoginPage; 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细观察，我从我们的`signup.jsx`中复制了整个东西，删除了`name`字段，并将`POST`的 URL 改为`api/authenticate`。

逻辑是一样的。如果用户成功登录，我们仍然会重定向用户。

启动服务器并导航到`/login`查看我们的结果。

成功了吗？如果是的话，太棒了！我们设法完成了项目的大部分。

### 使用会话确定用户身份

回想一下，我们在`req.session`中设置了一个 userId。我们能做的就是用一个中间件在我们的 MongoDB 数据库中查找它。

[![37fk3o](img/c957094e2f1b2755df9aadb89bf49dc7.png)](//images.ctfassets.net/rgbpfpd0hrww/2LuHOFvaeAX3JP2h8ZbWnp/813901ac6d5883d349c0243eab13f0d7/37fk3o.jpg)

#### `req.user`中间件

继续创建`middlewares/withAuthentication` :

```
import { ObjectId } from 'mongodb';

const withAuthentication = handler => (req, res) => {
  if (req.session.userId) {
    console.log(req.session.userId);
    return req.db.collection('users').findOne(ObjectId(req.session.userId))
      .then((user) => {
        console.log(user);
        if (user) req.user = user;
        return handler(req, res);
      });
  }
  return handler(req, res);
};

export default withAuthentication; 
```

Enter fullscreen mode Exit fullscreen mode

包含在我们的`withMiddleware.js` :
中

```
const middleware = handler => withDatabase(withSession(withAuthentication(handler))); 
```

Enter fullscreen mode Exit fullscreen mode

我提到顺序很重要，因为我们需要`req.session`在那时准备好。

如果有一个`req.session.userId`，我们尝试查找 id(确保首先将它转换成一个`ObjectId`)。如果有用户，我们就把它附加到`req`上。为什么？因为我们可能会在其他端点中重用这个`user`文档。由于每个端点都要经过`withAuthentication`，这可以通过`withMiddleware`获得，我们可以通过简单地引用`req.user`来确定用户。这也有助于我们避免重复代码。

#### 获取当前用户的会话端点

让我们有一个获取当前用户的端点。我会把它叫做`/api/session`，但是你可以把它叫做`/api/user`或者`/api/users/me`。

在`/api/session`中，输入以下内容:

```
import withMiddleware from '../../middlewares/withMiddleware';

const handler = (req, res) => {
  if (req.method === 'GET') {
    if (req.user) {
      const { name, email } = req.user;
      return res.status(200).send({
        status: 'ok',
        data: {
          isLoggedIn: true,
          user: { name, email },
        },
      });
    }
    return res.status(200).send({
      status: 'ok',
      data: {
        isLoggedIn: false,
        user: {},
      },
    });
  }
  return res.status(405).end();
};

export default withMiddleware(handler); 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，让端点接受获取当前用户信息的`GET`请求是合适的。

如您所见，我首先通过检查`req.user`是否存在来查看用户是否登录。

如果是这样，我回复说`isLoggedIn: true`，并回复用户名和电子邮件。

如果没有，我只是简单地回应说`isLoggedIn: false`和一个空的用户对象。

#### 用户上下文

我们需要从某个地方调用`GET /api/session`,并在 React 组件之间传递数据。我设法使用 React [**上下文 API**](https://reactjs.org/docs/context.html) 来实现它。

> 上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

是的，正是我们需要的。

另一个类似的解决方案是——我相信你听过很多次——[**Redux**](https://redux.js.org/)。然而，我相信 **Redux** 是矫枉过正，*越简单越好*。

让我们创建`components/UserContext.js` :

```
import React, { createContext, useReducer, useEffect } from 'react';
import axios from 'axios';

const UserContext = createContext();

const reducer = (state, action) => {
  switch (action.type) {
    case 'set':
      return action.data;
    case 'clear':
      return {
        isLoggedIn: false,
        user: {},
      };
    default:
      throw new Error();
  }
};

const UserContextProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, { isLoggedIn: false, user: {} });
  const dispatchProxy = (action) => {
    switch (action.type) {
      case 'fetch':
        return axios.get('/api/session')
          .then(res => ({
            isLoggedIn: res.data.data.isLoggedIn,
            user: res.data.data.user,
          }))
          .then(({ isLoggedIn, user }) => {
            dispatch({
              type: 'set',
              data: { isLoggedIn, user },
            });
          });
      default:
        return dispatch(action);
    }
  };
  useEffect(() => {
    dispatchProxy({ type: 'fetch' });
  }, []);
  return (
    <UserContext.Provider value={{ state, dispatch: dispatchProxy }}>
      { children }
    </UserContext.Provider>
  );
};

const UserContextConsumer = UserContext.Consumer;

export { UserContext, UserContextProvider, UserContextConsumer }; 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事。我建议在 React 网站上阅读[上下文](https://reactjs.org/docs/context.html)，然后回到这里。

我们首先通过调用`createContext()`创建一个上下文。

然后我使用 React Hook:
创建了一个[缩减器](https://reactjs.org/docs/hooks-reference.html#usereducer)(我再次建议先做一些阅读)

```
const [state, dispatch] = useReducer(reducer, { isLoggedIn: false, user: {} }); 
```

Enter fullscreen mode Exit fullscreen mode

其中`{ isLoggedIn: false, user: {} }`是默认值，`reducer`如上定义:

```
const reducer = (state, action) => {
  switch (action.type) {
    case 'set':
      return action.data;
    case 'clear':
      return {
        isLoggedIn: false,
        user: {},
      };
    default:
      throw new Error();
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

返回的值将被设置为`state`。

例如，在`clear`函数中，我简单地返回了空的用户对象和`isLoggedIn: false`。

事情在`set`变得有点奇怪。

减速器**不**支持异步功能，所以我必须实现一个小技巧。我有一个“代理”功能。所有减速器将首先通过`dispatchProxy`。

如果缩减器需要一个异步调用，比如`fetch` `dispatchProxy()`将执行异步动作，并在动作完成时调用`dispatch()`。特别是，在获取完成后，它`dispatchProxy()`调用`dispatch()`并更新用户上下文。

如果 reducer 不包含异步调用，我只需将它转发给实际的`dispatch()`。

每次我需要获取用户数据时(登录后，或更改配置文件后，等等)。)，我简单的调用了`dispatch()`，简单的导入`UserContext`就可以了。另请注意`useEffect(() => {}, []);`:我想在应用程序中获取第一个渲染。

类似于`reducer`的可用性，我们也可以通过导入`UserContext`来访问`state`。`state`将包含我们需要的所有用户信息。

##### 提供者

为了在子组件上使用上下文，我们需要在高阶组件上有一个 [ContextProvider](https://reactjs.org/docs/context.html#contextprovider) 。

```
<Provider>
  <Child1 />
  <Child 2>
    <Child 3 />
  </Child 2>
</Provider> 
```

Enter fullscreen mode Exit fullscreen mode

查看上面的映射，`Child 1`、`Child 2`、`Child 3`将能够访问上下文。`<Provider>`的格式如下:

```
<MyContext.Provider value={/* some value */}> 
```

Enter fullscreen mode Exit fullscreen mode

如果你回头看看`UserContext.jsx`中`UserContextProvider`的返回值，你可以看到我们实现了那个。因此，只需在最高阶组件中导入`UserContextProvider`。

在 Next.js 中， [`_app.js`](https://nextjs.org/docs#custom-app) 是我们能接触到的最高组件。因此，我们将把我们的`UserContextProvider`进口到那里。

创建`pages/_app.jsx` :

```
import React from 'react';
import App, { Container } from 'next/app';
import { UserContextProvider } from '../components/UserContext';

class MyApp extends App {
  render() {
    const { Component, pageProps } = this.props;
    return (
      <Container>
        <UserContextProvider>
          <Component {...pageProps} />
        </UserContextProvider>
      </Container>
    );
  }
}

export default MyApp; 
```

Enter fullscreen mode Exit fullscreen mode

##### 访问`UserContext`的`state`

打开我们的`pages/index.js`并填写以下内容:

```
import React, { useContext } from 'react';
import Link from 'next/link';
import { UserContext } from '../components/UserContext';
import Layout from '../components/layout';

const IndexPage = () => {
  const { state: { isLoggedIn, user: { name } } } = useContext(UserContext);

  return (
    <Layout>
      <div>
        <h1>
          Hello,
          {'  '}
          {(isLoggedIn ? name : 'stranger.')}
        </h1>

        {(!isLoggedIn ? (
          <>
            <Link href="/login"><div><button>Login</button></div></Link>
            <Link href="/signup"><div><button>Sign up</button></div></Link>
          </>
        ) : <button>Logout</button>)}

      </div>
    </Layout>
  );
};

export default IndexPage; 
```

Enter fullscreen mode Exit fullscreen mode

抱歉，如果`const { state: { isLoggedIn, user: { name } } } = useContext(UserContext);`因为我使用了一些 ES6 语法而让你感到困惑。我基本上是从`UserContext`的`state`得到`isLoggedIn`和`user.name`。

我也写一个条件渲染。如果`isLoggedIn === true`，我会渲染`name`。否则，我会渲染`"stranger"`。还有，欢迎文字下面，如果`isLoggedIn === false`，我也渲染两个链接到**登录**和**注册**。类似地，如果`isLoggedIn === true`，我们渲染`Logout`按钮。

##### [中的](#dispatch-raw-fetch-endraw-in-raw-usercontext-endraw-)将`fetch`中的`UserContext`调出来

记住，我们需要调度`fetch`减速器来让一切工作。

打开`pages/login.jsx`和`pages/signup.jsx`并包括`UserContext`。下面是`pages/login.jsx`的实现。试着自己做另一个。

```
axioswal
      .post('/api/authenticate', {
        email,
        password,
      })
      .then((data) => {
        if (data.status === 'ok') {
          //  Fetch the user data for UserContext here
          dispatch({ type: 'fetch' });
          redirectTo('/');
        }
      }); 
```

Enter fullscreen mode Exit fullscreen mode

`dispatch({ type: 'fetch' });`将调用`dispatchProxy()`，T1 发出`GET`请求并调用`dispatch()`更新`UserContext`。

### 注销:从`session`中删除`userId`

让我们给`index.jsx` :
中的**注销**按钮添加功能

```
import React, { useContext } from 'react';
import Link from 'next/link';
import axioswal from 'axioswal';
import { UserContext } from '../components/UserContext';
import Layout from '../components/layout';

const IndexPage = () => {
  const { state: { isLoggedIn, user: { name } }, dispatch } = useContext(UserContext);
  const handleLogout = (event) => {
    event.preventDefault();
    axioswal
      .delete('/api/session')
      .then((data) => {
        if (data.status === 'ok') {
          dispatch({ type: 'clear' });
        }
      });
  };
  return (
    <Layout>
      <div>
        <h1>
          Hello,
          {'  '}
          {(isLoggedIn ? name : 'stranger.')}
        </h1>

        {(!isLoggedIn ? (
          <>
            <Link href="/login"><div><button>Login</button></div></Link>
            <Link href="/signup"><div><button>Sign up</button></div></Link>
          </>
        ) : <button onClick={handleLogout}>Logout</button>)}

      </div>
    </Layout>
  );
};

export default IndexPage; 
```

Enter fullscreen mode Exit fullscreen mode

我从`UserContext`导入`dispatch`。另外，我给**注销**按钮分配了一个功能，该按钮向`/api/session`发出`DELETE`请求。如果请求成功，我们调用`clear`减速器清空`UserContext`。

很明显，我们需要在`api/session.js` :
中添加一个删除请求处理程序

```
import withMiddleware from '../../middlewares/withMiddleware';

const handler = (req, res) => {
  if (req.method === 'GET') {
    if (req.user) {
      const { name, email } = req.user;
      return res.status(200).send({
        status: 'ok',
        data: {
          isLoggedIn: true,
          user: { name, email },
        },
      });
    }
    return res.status(200).send({
      status: 'ok',
      data: {
        isLoggedIn: false,
        user: {},
      },
    });
  }
  if (req.method === 'DELETE') {
    delete req.session.userId;
    return res.status(200).send({
      status: 'ok',
      data: {
        isLoggedIn: false,
        message: 'You have been logged out.',
      },
    });
  }
  return res.status(405).end();
};

export default withMiddleware(handler); 
```

Enter fullscreen mode Exit fullscreen mode

就像我们登录时将`userId`设置为会话一样。我所要做的就是从会话中删除`userId`来注销。

## 结论

好吧，让我们运行我们的应用程序并进行测试。这将是使用 **Next.js** 和 **MongoDB** 构建一个成熟应用的第一步。

我希望这能成为你发布下一个伟大应用的样板。同样，在这里查看存储库[。我接受功能请求。下次应该增加什么功能？请随意制造问题并告诉我。](https://github.com/hoangvvo/nextjs-mongodb-app)

我不太擅长写作，所以如果文章有问题，请帮我改进。

祝你的下一个 Next.js + MongoDB 项目好运！