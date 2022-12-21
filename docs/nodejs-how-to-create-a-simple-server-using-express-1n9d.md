# node.js 如何使用 express 创建简单的服务器

> 原文：<https://dev.to/miku86/nodejs-how-to-create-a-simple-server-using-express-1n9d>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

我们还学习了如何使用 Node 的 HTTP 模块创建一个简单的服务器。

我们也知道[如何获得外部包](https://dev.to/miku86/nodejs-how-to-get-external-packages-npm-56oj)。

现在我们想学习如何使用 [express](https://www.npmjs.com/package/express) 创建一个简单的服务器。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
// import express (after npm install express)
const express = require('express');

// create new express app and save it as "app"
const app = express();

// server configuration
const PORT = 8080;

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

**注**:这个简单的服务器只有一条工作路线(`/`)。如果你想了解更多关于路由的信息，[请阅读](https://expressjs.com/en/guide/routing.html)的路由文档。

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
Server running at: http://localhost:8080/ 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以单击该链接并访问您创建的服务器。

* * *

## 进一步阅读

*   [快递](https://www.npmjs.com/package/express)
*   [路由](https://expressjs.com/en/guide/routing.html)
*   [响应方式](https://expressjs.com/en/guide/routing.html#response-methods)

* * *

## 提问

*   你用的是`express`还是一些像 [koa](https://www.npmjs.com/package/koa) 或者 [sails](https://www.npmjs.com/package/sails) 这样的库？你为什么用它？