# Nodejs Part 开发入门第 2 部分(中间件)

> 原文：<https://dev.to/rubiin/rest-api-development-part-2-middlewares-245f>

上次我们学习了 express web 框架的基础知识，比如如何设置路线、获取和发布请求等等。在这一部分，我们将讨论中间件，它为 express framework 增加了更多的功能。中间件功能是可以访问请求对象(req)、响应对象(res)以及应用程序的请求-响应周期中的下一个中间件功能的功能。如果你还没有忘记最后一部分，我们使用了 body-parser 和 morgan。这些是第三方中间件、快速报价的示例。

中间件经常在 Express.js 框架的上下文中使用，并且是 node.js 的一个基本概念。简而言之，它基本上是一个可以访问应用程序的请求和响应对象的函数。我认为，在应用程序处理请求之前，请求要经过一系列“检查/预筛选”。例如，中间件非常适合在请求进入应用程序之前确定请求是否经过身份验证，如果请求没有经过身份验证，则返回登录页面，或者记录每个请求。许多第三方中间件可以实现各种功能。

简单中间件示例:

```
var app = express();
app.use(function(req,res,next)){
    console.log("Request URL - "req.url);
    next();
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将为每个进来的请求执行，并将记录请求 url，next()方法本质上允许程序继续。如果没有调用 next()函数，程序将不会继续运行，并将在中间件执行时停止。

根据用途，Expressjs 将它们分为以下类型:

*   应用层中间件
*   路由器级中间件
*   错误处理中间件
*   内置中间件
*   第三方中间件

## 应用级中间件

把它想象成一个全局中间件，也就是说，你的应用程序中的所有路由都要经过这个中间件。。应用程序级中间件，这就是我们在上面的例子中使用的，因为当我们使用 app.use 时，我们说整个应用程序实例将在这里使用这个中间件。无论是什么路由器，只要有请求进来，它都会运行。当您想要在应用程序中每条路线上强加某些东西时，例如身份验证、日志记录等等，这种类型的中间件非常方便。

**举例:**

```
app.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 路由器级中间件

路由器级中间件的工作方式与应用级中间件相同，只是它被绑定到 express 的一个实例。路由器()。

常量路由器= express。Router()
使用 Router.use()和 router 加载路由器级中间件。方法()函数。

**举例:**

```
const express = require('express');

const app = express();

const router = express.Router()

router.use((req,res,next)=>{
 console.log('Time:', Date.now())
  next()
}) 
```

Enter fullscreen mode Exit fullscreen mode

上述中间件和这个中间件的区别在于，只有路由器中定义的路由才会使用这个中间件。

## 错误处理中间件

错误处理中间件与常规中间件略有不同。像所有其他中间件一样，它是一个函数，只是它接受了一个额外的参数，这是一个错误。作为第一个论点，你必须把它放在那里。你不放在那里，express 不会认为你在做一个错误的中间件。就像，这是普通的中间件。如果你把错误，请求，响应，然后下一个。现在，表达你正在犯的错误。

**举例:**

```
 app.use(function (err, req, res, next) {
  console.error(err.stack)
  res.status(500).send('Something broke!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 内置中间件

从 4.x 版开始，Express 不再依赖于 Connect。以前包含在 Express 中的中间件功能现在位于单独的模块中；请参见中间件功能列表。

Express 具有以下内置中间件功能:

*   express.static 服务于静态资产，比如 HTML 文件、图像等等。
*   express.json 使用 json 有效负载解析传入的请求。注意:在 Express 4.16.0+中可用
*   express.urlencoded 使用 URL 编码的有效负载解析传入的请求。注意:在 Express 4.16.0+中可用

## 第三方中间件

在某些情况下，我们将增加一些额外的功能到我们的后端。这些就是第三方中间件发挥作用的情况。还记得 morgan 和 body-parser 吗？他们是这些的例子

**示例:**主体解析器

当 Content-Type 请求头时，所有中间件都将使用解析的正文填充 req.body 属性。

**app . use({ urlencoded:false })**

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();

app.use(bodyParser.urlencoded({extended:false}))

app.use(bodyParser.json())

app.post('/save',(req,res)=>{
  res.json({
    "status":true,
    "payload":req.body
  })
}

app.listen(3000,(req,res)=>{
    console.log('server running on port')
}) 
```

Enter fullscreen mode Exit fullscreen mode

有关 Express 常用的第三方中间件功能的部分列表，请参见:第三方[中间件](https://expressjs.com/en/resources/middleware.html)。

使用中间件时要记住几件事:

*   应用程序中中间件的顺序很重要，因为请求会按顺序通过每个中间件。
*   忘记在您的中间件函数中调用 next()方法可能会暂停您的请求的处理。
*   对中间件函数中 req 和 res 对象的任何更改，都将使应用程序中使用 req 和 res 的其他部分可以使用这些更改

在下一篇文章中，我们将会关注在 express 上呈现编译后的 html 的模板引擎。