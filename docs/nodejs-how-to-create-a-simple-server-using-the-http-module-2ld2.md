# NodeJS:如何使用 HTTP 模块创建一个简单的服务器

> 原文：<https://dev.to/miku86/nodejs-how-to-create-a-simple-server-using-the-http-module-2ld2>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

现在我们想学习如何使用 [HTTP 模块](https://nodejs.org/api/http.html)创建一个*简单服务器*。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
// import http module
const http = require('http');

// server configuration
const HOST = '127.0.0.1';
const PORT = 8080;

// create the server
const server = http.createServer((req, res) => {
  res.end('Hello!');
});

// make the server listen to requests
server.listen(PORT, HOST, () => {
  console.log(`Server running at: http://${HOST}:${PORT}/`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

**注**:这是一个*极其简单的服务器*。我建议你[阅读 HTTP 模块](https://nodejs.org/api/http.html)的文档，尤其是报头如何工作以及如何发送。

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
Server running at: http://127.0.0.1:8080/ 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以单击该链接并访问您创建的服务器。

* * *

## 进一步阅读

*   [HTTP 模块](https://nodejs.org/api/http.html)
*   [HTTP 事务的剖析](https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction)
*   [HTTP/2 模块](https://nodejs.org/api/http2.html)
*   [HTTPS 模块](https://nodejs.org/api/https.html)

* * *

## 提问

*   你用的是原生的`HTTP/HTTPS`模块还是一些像 [express](https://www.npmjs.com/package/express) 这样的库？你为什么用它？