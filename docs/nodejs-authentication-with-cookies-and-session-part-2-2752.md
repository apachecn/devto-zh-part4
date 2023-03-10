# NodeJs &用 Cookies 和会话进行身份验证(第 2 部分)

> 原文：<https://dev.to/edemagbenyo/nodejs-authentication-with-cookies-and-session-part-2-2752>

## 简介

在本系列的上一篇文章中，我们研究了如何使用 HTTP Basic Auth 实现身份验证。这是一个非常简单的过程。请求身份验证的资源迫使用户在每次尝试访问它们之前输入用户名和密码。虽然这是保护信息的一种很好的方式，但是我们可以使用另一种机制来记住用户凭证。这将防止他们重复输入凭据。在这篇文章中，我们将看看如何使用 cookies 和会话来保存浏览器上的用户信息，并迫使服务器在后续请求中使用它们。

## 关于 Cookies 和会话

HTTP cookie (web cookie，浏览器 cookie)是从 web 服务器发送并保存在浏览器上的一小段数据，它会自动包含在发送到特定服务器的请求中。将来对服务器的请求将在头中包含 cookie。cookie 可以包含过期时间，过期后 cookie 将不再有效。你可以在这里阅读更多关于饼干的信息

在本帖中，我们将使用 express `cookie-parser`在浏览器中设置和检索 cookies。你可以在这里阅读更多关于快递`cookie-parser`如何运作[的信息](https://expressjs.com/en/resources/middleware/cookie-parser.html)

## 设置发展阶段

首先，我们将安装`cookie-parser`节点包，它将用于管理我们 express 服务器上的 cookies。我们通过从终端运行以下命令来实现这一点(确保终端已在您的工作文件夹中打开)。因为这是我们上一篇文章的延续，我们从同一个目录开始工作。我们将运行`$ npm install cookie-parser --save`来安装我们的包。

### 用 cookie 解析器认证

安装完`cookie-parser`后，我们用下面一行代码将其作为中间件包含进来。

```
const cookierParser = require('cookie-parser')

...

app.use(cookierParser('abcdef-12345')) 
```

Enter fullscreen mode Exit fullscreen mode

我们向`cookieParser`方法传递了一个字符串，因为我们希望我们的 cookie 被签名。传递的字符串用作签署 cookie 的秘密。一旦服务器在浏览器中设置了 cookie，cookie 信息将包含在每个后续请求的`signedCookies`属性中。
我们的 auth.js 文件现在看起来像这样。

```
function auth(req, res, next) {
  if (!req.signedCookies.user) {
    var authHeader = req.headers.authorization;
    if (!authHeader) {
      var err = new Error("You are not authenticated");

      res.setHeader("WWW-Authenticate", "Basic");
      err.status = 401;
      next(err);
    }

    var auth = new Buffer.from(authHeader.split("  ")[1], "base64")
      .toString()
      .split(":");
    var username = auth[0];
    var password = auth[1];

    if (username == "admin" && password == "p@ssword") {
        res.cookie('user','admin',{
            signed:true,

        });
      next();
    } else {
      var err = new Error("You are not authenticated");

      res.setHeader("WWW-Authenticate", "Basic");
      err.status = 401;
      next(err);
    }
  }else{
      if(req.signedCookies.user == 'admin'){
          next();
      }else{
        var err = new Error("You are not authenticated");
        err.status = 401;
        next(err);
      }
  }
}

module.exports = auth; 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们检查请求的`signedCookies`属性是否有一个`user`属性。用户被迫使用 HTTP Basic 提供用户名和密码，这是我们在上一篇文章中实现的；否则，用户将被授予对资源的访问权限。

我们可以通过从终端`$ npm run start`执行下面的命令来启动服务器，从而测试我们的 cookie-parser 实现。任何访问`localhost:3001/secret`的尝试都会提示用户输入其凭证。当凭据与所需的凭据匹配时，用户将被带到秘密页面。这里我们会注意到的一件事是浏览器中设置了一个 cookie。授予对/secret 页面的后续访问权限时，不会提示您输入用户凭据。

### 会话认证

尽管 cookie 提供了一种在浏览器上保留用户信息并将其包含在任何请求中的方法，但它们有一些局限性。其中之一是它们无法保存大量数据。我们使用 cookies 和会话来跟踪服务器端的用户信息。cookie 保存会话 id，每当向服务器发出请求时，服务器都会检索会话 id，并使用它来获取服务器上的用户信息。默认情况下，会话信息存储在内存中，每当服务器重新启动时就会被清除。为了解决这个问题，我们使用文件存储或永久数据库技术。Express 使用`express-session`节点包来管理会话，使用`session-file-store`将会话数据存储在会话文件中。你可以在这里`session-file-store`[这里](https://www.npmjs.com/package/session-file-store)阅读更多关于`express-session` [。](https://expressjs.com/en/resources/middleware/session.html)

在我们开始之前，我们将继续我们之前的代码，可以在这里找到。派生项目并继续安装所需的包。

为了便于我们使用`express-session`和`session-file-store`，我们将它们包含在 index.js 文件中。

```
...
// const cookierParser = require('cookie-parser')
const session = require('express-session')
const FileStore = require('session-file-store')(session)

...

app.use(session({
    name:'session-id',
    secret:'123456xxx',
    saveUninitialized:false,
    resave:false,
    store:new FileStore()
}))

... 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们初始化了会话，并使用会话文件存储机制来存储会话数据。这就是我们在会话中需要做的全部工作。在我们的 auth.js 文件中，我们用 session 替换了 cookies 的使用。这将给我们

```
...
  if (!req.session.user) {

...

if (username == "admin" && password == "p@ssword") {
        req.session.user = 'admin'
      next();
    } else {
      var err = new Error("You are not authenticated");

      res.setHeader("WWW-Authenticate", "Basic");
      err.status = 401;
      next(err);
    }
}else{
      if(req.session.user == 'admin'){
          next();
      }else{
        var err = new Error("You are not authenticated");
        err.status = 401;
        next(err);
      }
  } 
```

Enter fullscreen mode Exit fullscreen mode

完成这些更改后，重启服务器并转到`locahost:3001/secret`。系统将提示您提供凭据。完成后，您会注意到在您的工作文件夹中会创建一个名为`sessions`的文件夹。只要凭据有效，该文件夹将包含会话数据并帮助您访问页面。

## 结论

在这篇文章中，我们实现了一种机制，首先使用 cookies 在浏览器上保留用户凭证，然后我们看看如何使用会话让服务器记住用户信息。这是防止用户每次尝试访问受保护资源时都输入相同凭据的好方法。在下一篇文章中，我们将关注用户注册，使用会话登录。

参考
[MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)