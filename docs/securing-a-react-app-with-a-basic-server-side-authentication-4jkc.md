# 使用基本的服务器端身份验证保护 React 应用程序

> 原文：<https://dev.to/bnevilleoneill/securing-a-react-app-with-a-basic-server-side-authentication-4jkc>

[![](img/6645f472dc516f5b1a77e66731e2a6d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NP4sol9D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k109oi5onzwbpi79xqr1.png)

众所周知，客户端由于其暴露的本质而不安全。在您的 web 应用程序中，您可以有条件地呈现视图以向不同的用户显示不同的内容，但是如果该信息已经存储在客户端，它就不再安全。

为了确保只有具有凭证的用户才能看到受限的内容，您应该在身份验证时从服务器发送内容数据。

本文将带您了解如何通过对一个 [Express 服务器](https://expressjs.com)的基本认证来实现这一点。虽然客户端将是一个 React 应用程序，但您可以将其应用于任何其他客户端应用程序。

## 基础知识

在本文中，我假设您已经知道如何从头开始创建和构建 React 项目，因此我将主要关注服务器端的实现。

引导 React 项目最简单的方法显然是使用`create-react-app`包。当您用这个包创建一个项目，然后运行`npm start`时，您基本上启动了一个 Webpack 服务器。这在您的本地机器上工作得很好，但是当您想要将它部署到远程服务器时，您需要自己的服务器来服务您的 React 应用程序，它基本上是 HTML、JavaScript 和 CSS 的包。

我将引用本示例项目的以下文件夹结构:

```
--- Project Folder
 |__ client (React App)
 |__ server.js
 |__ package.json 
```

Enter fullscreen mode Exit fullscreen mode

因此，有一个`Project Folder`，在它里面，我们有一个包含 React 应用程序的`client`文件夹，还有一个`server.js`和`package.json`文件，您可以在项目目录中的终端上使用以下命令创建它们。

```
npm init -y
touch server.js 
```

Enter fullscreen mode Exit fullscreen mode

## 服务 React app

### 如何代理 React app

您部署的 React 应用程序将被构建，`build`文件夹将由 Express 服务器提供服务。然而，当在本地开发 React 应用程序时，您不应该在每一个变化上都为生产而构建。为了避免这种情况，您可以将 React 应用程序代理到某个端口，这样您就可以使用内置的 Webpack 服务器在本地运行 React 应用程序，并且仍然可以与 Express 服务器进行通信。

为此，您应该在 React 应用程序的`project.json`文件中添加以下行，假设 Express server 将在端口 5000 上提供服务。

```
“proxy”: “http://localhost:5000/" 
```

Enter fullscreen mode Exit fullscreen mode

### 服务于构建文件夹

express 服务器应该为构建文件夹提供服务，该文件夹将在部署到远程服务器的过程中创建。

下面的代码片段是一个基本的 Express 服务器。我们将在此基础上增加认证和其他功能。

```
const express = require('express');
const path = require('path');
const app = express();

const PORT = process.env.PORT || 5000;

app
  .use(express.static(path.join(__dirname, '/client/build')))
  .listen(PORT, () => console.log(`Listening on ${PORT}`));

app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, '/client/build/index.html'));
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 本地运行

如前所述，React 应用程序仍将使用 Webpack 服务器，因为它将代理端口 5000。但是，我们仍然需要单独运行 Express 服务器。

对于运行和监听更改来说，package 非常方便，因此您可以全局安装它，然后只需在项目文件夹的主目录中运行以下命令就可以运行服务器。

```
nodemon server.js 
```

Enter fullscreen mode Exit fullscreen mode

至于 React 应用程序，我们只需在**客户端**文件夹中运行以下命令。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

### 如何在远程服务器上运行

虽然这是一个可选的步骤，但值得一提的是。让我们假设我们想要将我们的应用程序部署到一个 [Heroku](https://www.heroku.com) dyno。

Heroku 检测一个 NodeJS 应用程序，安装依赖项并自动运行它，但您仍然需要告诉它进入特定的文件夹，安装依赖项并为生产构建 React 应用程序，在我们的例子中，这将分别进入`/client`运行`npm install`和`npm run build`。

为此，Heroku 有一个构建后命令:

```
"heroku-postbuild": "cd client && npm install && npm run build" 
```

Enter fullscreen mode Exit fullscreen mode

将此添加到服务器的`package.json`中的`"scripts"`键下。

另外，确保 NodeJS 应用程序的入口点是在`package.json`文件中的`server.js`。如果你用`-y`标志初始化你的 npm 包为`npm init -y`，这很可能是`index.js`。

```
"main": "server.js" 
```

Enter fullscreen mode Exit fullscreen mode

## 基本认证

顾名思义,`express-basic-auth`是一个非常方便易用的包，用于基本的身份验证。

安装软件包，然后要求它位于您的`server.js`的顶部。然后，我们通过使用包的实例来定义凭证。

```
const basicAuth = require('express-basic-auth');

const auth = basicAuth({
  users: {
    admin: '123',
    user: '456',
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当`auth`变量被用作一个端点的参数时，当且仅当与请求一起发送的凭证匹配时，来自该端点的响应才会返回到客户端。

在下面显示的代码中，可以看到服务器端的`/authenticate`端点和客户端发送的`GET`请求以及包含凭证的`auth`对象。

```
// End-point on Server

app.get('/authenticate', auth, (req, res) => {
  if (req.auth.user === 'admin') {
    res.send('admin');
  } else if (req.auth.user === 'user') {
    res.send('user');
  }
});

// Request on Client

const auth = async () => {
  try {
    const res = await axios.get('/authenticate', { auth: { username: 'admin', password: '123' } });
    console.log(res.data);
  } catch (e) {
    console.log(e);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

看一下上面的例子，传递正确的凭证会根据使用的用户名将`admin`或`user`作为字符串响应发送回来。错误的凭证只是返回一个`401 (Unauthorized)`的响应。

到目前为止，我们已经弄清楚了如何在凭证正确的情况下将数据从服务器发送到客户端。所以，现在下一步是通过一个 [cookie 会话](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)来持久化该认证。

我们可以在客户机上从服务器设置一个 cookie，而不是从`authenticate`端点发送响应。通过部署另一个端点，我们可以检查 cookie，并实际发送数据来填充视图。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### Cookie-session

一旦用户通过身份验证，这些信息应该存储在客户端的某个地方，这样用户就不用每次都进行身份验证。通常的做法是使用 cookies 来存储这个会话信息。只要设置了正确的标志，Cookies 就是安全的。

**httpOnly:** 这个标志确保客户端脚本不能访问 cookie，但是服务器可以。

**secure:** 该标志确保 cookie 信息通过 HTTPS 协议与加密请求一起发送到服务器。

使用`secure`标志时，还需要一个密钥来对 cookie 进行签名。为此，我们使用`cookie-parser`中间件作为 Express server。

一个 cookie 仅仅有一个**名称**和一个**值**。即使有上述标志，也不要在 cookie 参数中泄露任何易受攻击的信息。

在下面显示的代码中，您可以看到`server.js`，它在认证时设置一个唯一的 cookie。

正如您在设置 cookie 后所看到的，响应还发送了一个带有`screen:admin`或`screen:user`键/值对的对象。

这个响应稍后将在客户端的 React 应用程序中使用。

```
const cookieParser = require('cookie-parser');

// A random key for signing the cookie
app.use(cookieParser('82e4e438a0705fabf61f9854e3b575af'));

app.get('/authenticate', auth, (req, res) => {
  const options = {
    httpOnly: true,
    signed: true,
  };

  if (req.auth.user === 'admin') {
    res.cookie('name', 'admin', options).send({ screen: 'admin' });
  } else if (req.auth.user === 'user') {
    res.cookie('name', 'user', options).send({ screen: 'user' });
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

由于 cookie 有一个`httpOnly`标志，我们既不能在客户端读取也不能删除它。因此，我们还需要两个端点来读取和删除 cookie，并相应地发送回响应。

### 如何从服务器读取/删除 cookie

从服务器中读取和删除 cookie 非常简单，但是您应该记住，这些功能的端点不应该有`auth`变量，因为不需要对这些端点进行身份验证。

所以下面我们有两个端点；`/read-cookie`和`/clear-cookie`。

带有`res`的`signedCookies`对象包含我们为 cookie 设置的`name:value`对。

```
res.cookie(‘name’, ‘admin’, options) 
```

Enter fullscreen mode Exit fullscreen mode

因此，根据 cookie 名称的值，我们发送一个响应。

至于端点`/clear-cookie`，删除 cookie 只需参考 cookie 的名称，即`name`。

```
app.get('/read-cookie', (req, res) => {
  if (req.signedCookies.name === 'admin') {
    res.send({ screen: 'admin' });
  } else if (req.signedCookies.name === 'user') {
    res.send({ screen: 'user' });
  } else {
    res.send({ screen: 'auth' });
  }
});

app.get('/clear-cookie', (req, res) => {
  res.clearCookie('name').end();
}); 
```

Enter fullscreen mode Exit fullscreen mode

按照这个逻辑，您可以创建几个不同的端点，根据您的应用程序发送不同类型的数据。您需要做的就是检查 cookie 并相应地发送响应。

下面您可以找到完整的`server.js`文件，它服务于客户端 React 应用程序，这将在下一节中介绍。

```
const express = require('express');
const basicAuth = require('express-basic-auth');
const cookieParser = require('cookie-parser');
const path = require('path');

const app = express();

const auth = basicAuth({
  users: {
    admin: '123',
    user: '456',
  },
});

const PORT = process.env.PORT || 5000;

app.use(cookieParser('82e4e438a0705fabf61f9854e3b575af'));

app
  .use(express.static(path.join(__dirname, '/client/build')))
  .listen(PORT, () => console.log(`Listening on ${PORT}`));

app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, '/client/build/index.html'));
});

app.get('/authenticate', auth, (req, res) => {
  const options = {
    httpOnly: true,
    signed: true,
  };

  console.log(req.auth.user);

  if (req.auth.user === 'admin') {
    res.cookie('name', 'admin', options).send({ screen: 'admin' });
  } else if (req.auth.user === 'user') {
    res.cookie('name', 'user', options).send({ screen: 'user' });
  }
});

app.get('/read-cookie', (req, res) => {
  console.log(req.signedCookies);
  if (req.signedCookies.name === 'admin') {
    res.send({ screen: 'admin' });
  } else if (req.signedCookies.name === 'user') {
    res.send({ screen: 'user' });
  } else {
    res.send({ screen: 'auth' });
  }
});

app.get('/clear-cookie', (req, res) => {
  res.clearCookie('name').end();
});

app.get('/get-data', (req, res) => {
  if (req.signedCookies.name === 'admin') {
    res.send('This is admin panel');
  } else if (req.signedCookies.name === 'user') {
    res.send('This is user data');
  } else {
    res.end();
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 一个 React app 的实际例子

假设你有一个**管理员**屏幕和一个普通的**用户**屏幕，你在上面显示不同的内容。

*   我们需要的第一件事是身份验证请求，我们将凭证发送到服务器。
*   我们需要从`componentDidMount`生命周期钩子发送的另一个请求来检查是否已经有一个 cookie，以便我们可以自动登录。
*   那么我们可能需要一些其他的请求来获取额外的数据。
*   最终，我们需要能够发送一个请求来清除 cookie，以便会话不再持续。

下面你可以找到完整的客户端代码。然而，为了让它工作，很明显，你应该在服务器旁边运行它。
[https://codesandbox.io/embed/o73bh](https://codesandbox.io/embed/o73bh)
让我们来过一遍 React app 的重要步骤。

我们有三个不同的状态变量。`screen`、`username`、`password`。

顾名思义，`username`和`password`用于存储输入的字段数据，并通过`auth`功能通过`/authenticate`端点发送给服务器。因此登录按钮的`onClick`事件调用了`auth`函数。只有在用户进行初始身份验证时，才需要这样做。

为了检查用户是否已经登录，在`readCookie`功能中使用了`/read-cookie`端点。这个函数在组件挂载时只调用一次。来自该端点的响应将`screen`状态设置为将视图切换到`admin`屏幕或`user`屏幕。

在这个例子中，`admin`和`user`屏幕是同一个组件，但是由于来自服务器的响应根据认证而改变，所以同一个组件呈现不同的内容。

此外，`/get-data` end-point 展示了使用来自服务器的 cookie 特定响应的另一个示例。

最后，`/clear-cookie`与注销按钮的`onClick`事件一起使用，以清除 cookie 并将`screen`状态变量设置回其初始状态。

## 结论

通读本文，您将了解到在带有`express-basic-auth` npm 包的 Express 服务器上进行基本的服务器端认证的概念。这种简单的身份验证系统的用例可以是任何类型的小型个人项目，或者是具有固定用户数量的界面的安全页面。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/441375fdfee1c124c713d0800bb5e0d5.png)](https://logrocket.com/signup/)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[使用基本的服务器端认证](https://blog.logrocket.com/securing-a-react-app/)保护 React 应用最先出现在[博客](https://blog.logrocket.com)上。