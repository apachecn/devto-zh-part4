# Nodejs 上的 Api 开发入门

> 原文：<https://dev.to/rubiin/getting-started-with-api-develpoment-on-nodejs-52bi>

在我们动手之前，我假设您对 js 有很好的了解。我还假设您的机器上安装了 nodejs。nodejs 有很多 web 框架。要指出几个流行的，我可能会说 express，koa，hapi。我们将使用 express，因为它是 nodejs 使用最多的 web 框架，有大量的模块被配置为使用它。语法和学习曲线也很容易掌握，如果遇到问题，stackoverflow 中的每个查询都有很多答案。
首先，让我们创建一个目录并转到该目录。进入项目目录后，打开终端/控制台并运行 npm init 来启动 nodejs 项目。细节随便填。完成后，输入

```
npm i — save express body-parser morgan nodemon 
```

Enter fullscreen mode Exit fullscreen mode

点击回车键，应该会安装并更新 package.json 文件中的依赖项。

**Nodemon** 将监视 node.js 应用程序中的任何更改，并自动重启服务器——这对于开发来说是完美的，因此您不必手动重启服务器来查看更改。

**Morgan** 是 node.js 的 HTTP 请求记录器中间件。

**Body-parser** 是 Node.js 体解析中间件。它在您的处理程序之前解析中间件中的传入请求体，在 req.body 属性下提供。

安装完所有模块后，在根文件夹中创建一个 app.js 文件，并在您喜欢的编辑器中打开它。我使用 visual studio 代码，但是你可以使用你喜欢的任何文本编辑器。
基本设置
在编辑器中打开 app.js，用
要求 express 模块

```
var express=require('express')
Now create an express instance with
var app= express() 
```

Enter fullscreen mode Exit fullscreen mode

## 路线

现在让我们定义一些路线。路由方法是从 HTTP 方法之一派生出来的，并附加到 express 类的一个实例上。
下面的代码是为 GET 和 POST 方法定义的到应用程序根的路由的例子。

```
 // GET method route

app.get('/', function (req, res) {
  res.send('GET request to the homepage')
})

// POST method route

app.post('/', function (req, res) {
  res.send('POST request to the homepage')
}) 
```

Enter fullscreen mode Exit fullscreen mode

`Express`支持对应于所有 HTTP 请求方法的方法:get、post 等等。有关完整列表，请参见 app.METHOD.
以下是一些基于字符串的路线路径示例。
此路由路径将请求与根路由/匹配。因此，如果你在 localhost:/中访问这个应用程序，你将在你的窗口上打印出 root】

```
app.get('/', function (req, res) {
  res.send('root')
}) 
```

Enter fullscreen mode Exit fullscreen mode

此路由路径将请求匹配到/关于。因此，如果你访问 localhost:/中的应用程序，你会在你的窗口上看到

```
app.get('/about', function (req, res) {
  res.send('about')
}) 
```

Enter fullscreen mode Exit fullscreen mode

您还可以在路线中使用正则表达式，使您的路线更加动态。

## 路线参数

要使用路由参数定义路由，只需在路由的路径中指定路由参数，如下所示。这将简单地输出所有参数，即 userId 和 bookId 作为一个对象。您也可以通过使用
req.params.paramname
来访问单个参数

```
app.get('/users/:userId/books/:bookId', function (req, res) {
  res.send(req.params)
}) 
```

Enter fullscreen mode Exit fullscreen mode

综上所述，下面的代码是一个非常基本的路线的例子。

```
var express = require('express')
var app = express()

// respond with "hello world" when a GET request is made to the homepage

app.get('/', function (req, res) {
  res.send('hello world')
})

app.get('/about', function (req, res) {
  res.send('about')
})

app.get('/:userId', function (req, res) {
  res.send(req.params.userId)
})

app.post('/post', function (req, res) {
  res.send('this is a post request')
})

// tell express to run on port 8000

app.listen(8000) 
```

Enter fullscreen mode Exit fullscreen mode

这部分到此为止。