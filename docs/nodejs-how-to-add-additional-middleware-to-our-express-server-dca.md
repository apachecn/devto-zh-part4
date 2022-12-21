# NodeJS:如何向我们的 Express 服务器添加额外的中间件

> 原文：<https://dev.to/miku86/nodejs-how-to-add-additional-middleware-to-our-express-server-dca>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

我们还学习了如何使用 express 创建一个简单的服务器。

现在我们想学习如何向我们的 express 服务器添加额外的中间件。

## 重用我们的简单服务器来自[上一篇文章](https://dev.to/miku86/nodejs-how-to-create-a-simple-server-using-express-1n9d)

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
const express = require('express');
const app = express();

const PORT = 8080;

app.get('/', (request, response) => {
  response.send('Hello World');
});

app.listen(PORT, () => {
  console.log(`Server running at: http://localhost:${PORT}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

**注**:这是我们的起点，我们有一条工作路线。
我更新了`req`到`request` & `res`到`response`以增加可读性。

* * *

## 附加中间件

什么是中间件？

有时，您希望在默认的请求-响应-周期之间添加额外的功能。假设您想要获得关于当前请求的一些详细信息。

您可以编写一个简单的中间件来处理这项任务，并将其添加到您的 express 应用程序中。

## 创建中间件

*   创建一个名为`logger.js`的文件:

```
touch logger.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
const logger = (req, res, next) => {
  console.log(`Time: ${new Date()} - Method: ${req.method} - Path: ${req.originalUrl}`);
  next();
};

module.exports = logger; 
```

Enter fullscreen mode Exit fullscreen mode

*   需要 index.js 中的新记录器并使用中间件:

```
// import express (after npm install express)
const express = require('express');

// import the logger
const logger = require('./logger');

// create new express app and save it as app
const app = express();

// server configuration
const PORT = 8080;

// use the middleware
app.use(logger);

// create a route for the app
app.get('/', (req, res) => {
  res.send('Hello World');
});

// make the server listen to requests
app.listen(PORT, () => {
  console.log(`Server running at: http://localhost:${PORT}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   访问: [http://localhost:8080](http://localhost:8080)

*   控制台结果:

```
Server running at: http://localhost:8080/
Time: Tue Sep 03 2019 17:32:30 GMT+0200 (Central European Summer Time) - Method: GET - Path: / 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## Express 和 PostgreSQL 上的系列

在这里你可以找到我的关于 Express 和 PostgreSQL 的系列文章

* * *

## 进一步阅读

*   [快递](https://www.npmjs.com/package/express)
*   [使用中间件](http://expressjs.com/en/guide/using-middleware.html)
*   [app.use()](https://expressjs.com/en/4x/api.html#app.use)
*   [中间件回调函数示例](https://expressjs.com/en/4x/api.html#middleware-callback-function-examples)

* * *

## 提问

*   你最喜欢的 express 中间件是什么？