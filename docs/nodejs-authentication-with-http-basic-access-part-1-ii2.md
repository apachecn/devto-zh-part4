# NodeJs &使用 Http 基本访问进行身份验证(第 1 部分)

> 原文：<https://dev.to/edemagbenyo/nodejs-authentication-with-http-basic-access-part-1-ii2>

## 简介

每个开发人员在开发过程的任何阶段都面临着不可避免的问题，那就是为他的应用程序实现一个认证特性。虽然这项任务已经变得更加容易，因为在大多数框架中有大量的第三方应用程序、依赖项和内置认证特性，但是我们很容易忘记看一看整个认证过程是如何工作的。在这一系列的文章中，我们将回顾 NodeJS 中可用的各种身份验证可能性。我们将首先从 HTTP 基本认证开始，然后使用 Cookies、会话、JWT 和 passport 来实现相同的认证。尽管它们都实现了相同的目标，但其中一个比另一个更安全。在处理每一种方法时，我们将看看每种方法的优缺点。

在这篇文章中，我们将看看如何在浏览器中使用最基本的身份验证工具 HTTP Basic Access 来实现身份验证。

## 关于 HTTP 基本认证

HTTP 基本身份验证是一种机制，在这种机制中，服务器询问任何请求信息的人，并以用户名和密码的形式得到响应。服务器接收的信息用 base-64 编码，并传递到授权头中。

当客户端向服务器上需要授权的资源发出请求时，服务器会发送一个响应，其中包含一个带有`WWW-Authenticate Basic`标头的`401`状态代码。大多数浏览器通过向用户请求用户名和密码来处理这种响应。
当 web 客户端获得用户名和密码后，它会以`Authorization: Basic username: password`的形式向服务器发回一个带有 ah 授权头的响应。
然而，正如我前面提到的，客户端提供的用户名和密码只使用 base-64 加密。不推荐使用这种认证用户的方法，因为当双方之间的连接不安全时，双方之间交换的信息可能会被截获。只有当客户端和服务器之间的连接安全时，HTTP 基本身份验证才是安全的。

有关 HTTP 基本认证的更多详细信息，请参见 [RFC 2617，HTTP 认证:基本和摘要访问认证](http://www.ietf.org/rfc/rfc2617.txt)

现在我们已经介绍了基本认证是如何工作的，让我们在 NodeJs 中实现它。

## 设置发展阶段

在我们继续编写任何代码之前，我们将首先安装应用程序的所有依赖项。第一个要安装的依赖项显然是 expressjs，然后我们会添加 morgan。

关于 morgan 和 body-parser 的简要说明；morgan 是一个用于 nodejs 的 HTTP 请求日志中间件，你可以在这里阅读更多关于 T0 的内容。

然后我们用这个`$ npm install express morgan body-parser --save`在一行中安装所有的依赖项。

## 引导服务器

安装完所有依赖项后，让我们在工作文件夹中添加一个新文件`index.js`。这是我们表演的地方。
首先，让我们启动服务器。我们用下面几行代码来实现这一点。

```
 const http = require('http');
const express = require('express');
const morgan = require('morgan');
const app = express();

app.use(morgan('dev'));

const server = http.createServer(app);

server.listen(3001,'localhost',()=>{
    console.log("Server is live at localhost:3001");
}); 
```

Enter fullscreen mode Exit fullscreen mode

保存文件，并使用您的终端，确保您在工作文件夹中运行`$ node index`。您应该会在终端中看到`Server is live at localhost:3001`。这是服务器启动并运行的标志，但是我们还没有任何路由。这就是我们接下来要做的。

```
...

app.use(morgan('dev'));

app.get('/',(req,res)=>{
    res.statusCode=200;
    res.end("Welcome to your express app!")
})

const server = http.createServer(app);

... 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码向我们的应用程序添加了一条默认路线，当我们导航到这条路线时会显示一条消息。停止终端中的服务器，然后再次启动它。这一次，我们应该能够在 web 浏览器`http://localhost:3001`中导航到端口 3001 处的本地主机，并看到消息`Welcome to your express app`。
让我们添加另一条路线，我们将为通过认证的会员设置一个密码。我们的秘密代码的路线将是`http://localhost:3001/secret`

```
...

app.use(morgan('dev'));

 ...

app.get('/secret',(req,res)=>{
    res.statusCode=200;
    res.end("******")
});
 ...

const server = http.createServer(app);

... 
```

Enter fullscreen mode Exit fullscreen mode

由于这是秘密信息，我们不希望每个人都访问它，但只有用户可以验证自己。
我们将在下一部分实施我们的认证。

## 添加 HTTP 基本访问认证

向路由添加身份验证非常简单。我们将其作为中间件添加到中，然后在继续路由之前对其进行检查。

我们用`use()`方法在 express 中添加中间件。因此，只有当请求通过了中间件中的检查时，在调用`use()`方法之后驻留的任何路由才会被访问。

为了添加我们的中间件，我们将在我们的工作目录中创建一个`auth.js`文件，在那里我们将实现我们的认证。

将以下代码添加到`auth.js`文件中。

```
function auth (req, res, next){
    var authHeader = req.headers.authorization;
    if(!authHeader){
        var err = new Error('You are not authenticated')

        res.setHeader('WWW-Authenticate','Basic');
        err.status = 401
        next(err)
    }

    var auth = new Buffer(authHeader.split('  ')[1], 'base64').toString().split(':')
    var username = auth[0]
    var password = auth[1]

    if(username == 'edemone' && password =='secret'){
        next();
    }else{
        var err = new Error('You are not authenticated')

        res.setHeader('WWW-Authenticate','Basic');
        err.status = 401
        next(err)
    }

}

module.exports = auth; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码部分首先检查授权头，当来自客户端的请求没有授权头时，用 401 状态代码和 WWW-Authenticate 基本头进行响应。然而，我们会继续检查授权头(如果有的话),看它是否包含用户名和密码，并对照预期的用户名和密码进行检查。在本例中，我们使用的是硬编码的用户名和密码，但是我们也可以实现对数据库中信息的检查。
当检查通过时，用户被授权访问，否则，我们抛出一个错误，并附带有 WWW-Authentication Basic 头和一个 401 状态代码。

现在需要将我们的身份验证检查附加到`/secret`路由上。我们在`index.js`文件中这样做。

```
...
const morgan = require('morgan');
const auth = require('./auth');

...

app.use(auth)
app.get('/secret',(req,res)=>{
    res.statusCode=200;
    res.end("******")
});

... 
```

Enter fullscreen mode Exit fullscreen mode

在`/secret`路由前添加`app.use(auth)`可以确保除非用户通过上述中间件，否则不授予访问权限。

重启服务器后，当我们试图访问`http://localhost/secret`时，浏览器会提示我们要求用户提供用户名和密码。当用户输入与预期匹配的用户名和密码时，用户将被发送到所请求的资源。

## 结论

在 NodeJS 认证系列的第一篇文章中，我们研究了如何使用 HTTP 基本认证来检查试图访问服务器上受保护资源的用户。从 web 客户端发送的信息使用 Base-64 编码。尽管不建议使用 HTTP 基本身份验证，因为客户端和服务器之间交换的信息可能会被截获。在我们的下一篇文章中，我们将看看如何使用 cookies 和会话实现相同的认证。

感谢阅读。请把你的想法写在评论区。这篇文章最初发布在我的[博客](http://edemagbenyo.com/nodejs/authentication/http/2019/07/29/nodejs-authentication-with-http-basic-auth.html)