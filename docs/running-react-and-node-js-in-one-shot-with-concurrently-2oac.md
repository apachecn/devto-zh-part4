# 同时运行 React 和 Node.js！

> 原文：<https://dev.to/numtostr/running-react-and-node-js-in-one-shot-with-concurrently-2oac>

经常在一个完整的项目上工作是令人生畏的。无论是个人项目还是专业项目，我经常发现自己处于同样的情况。当我开始使用 React 和 Node.js 时，首先想到的问题是，我甚至如何**连接**和**运行**这两者？

这将是由两部分组成的系列文章，我们将探讨在开发环境中运行 React 和 Node.js 的不同方法。

*   [衔接](#proxy-connection) & [兼营](#using-concurrently)(本岗位)
*   [使用 Docker](https://dev.to/numtostr/running-react-and-node-js-in-one-shot-with-docker-3o09) (第二岗位)

将 React 连接到 Node.js(也适用于任何后端)相当容易，通常称为 React 代理。您只需要在 React 项目的`package.json`中添加`proxy`字段，并将其指向您的开发后端服务器。

```
{  "proxy":  "http://localhost:5000"  } 
```

如果你想了解更多，请点击这里。

运行 React 和 Node.js 比连接更复杂。这将是由两部分组成的系列文章，我们将探讨并行运行这两者的不同方法。两种方法在许多方面都不同，例如使用的工具、文件夹结构。

### 兼用

[并发](https://www.npmjs.com/package/concurrently)是一个可以同时运行多个 npm 脚本的包。

#### ~初始设置

```
$ mkdir awesome_project && cd awesome_project && npm init -y 
```

这将在我们的项目中创建一个带有一些默认选项的`package.json`。

#### ~创建 react 应用

```
$ npx create-react-app client 
```

这将创建一个名为 *client* 的文件夹来保存我们的 react 应用程序。

#### ~某 Node.js 代码

在项目的根目录下创建`index.js`,如果没有服务器设置，粘贴下面的代码。

```
#!/usr/bin/env node 
const http = require("http");

// Port Environment variable
const PORT = process.env.PORT || 5000;

// Creating the node server
const SERVER = http.createServer();

// Firing up the server on selected port
SERVER.listen(PORT);

SERVER.on("listening", () => {
    console.log("[Server]::LISTEN:%s", PORT);
});

// Callback function for checking connecting or error
SERVER.on("error", error => {
    throw new Error(`[Server]::ERROR:${error.message}`);
}); 
```

#### ~并发安装

```
npm i -D concurrently 
```

为了让 React 和 Node 并发工作，我们必须在`package.json`中添加一些脚本。

```
{  "scripts":  {  "server":  "node index.js",  "client":  "npm start --prefix client",  "dev":  "concurrently \"npm run server\"  \"npm run client\""  }  } 
```

最后，我们将有一个类似这样的文件夹结构。

```
> awesome_project
    > node_modules
    > client # This is our react front-end and else will be our node.js backend
        > node_modules
        > src
        - package.json
    - index.js
    - package.json 
```

现在`dev`脚本将在我们的项目中运行 React 和 Node.js 应用程序，并且[代理](#proxy-connection)将连接它们。

```
$ npm run dev 
```

对于大多数开发人员来说，并发使用非常好(这也是我最初使用的方法)。但是它有一个我觉得很讨厌的问题，那就是**文件夹结构**(对你来说可能不同)。

在第二篇文章中，我们将关注码头工人的方式。所以，如果你不知道 docker 就呆在这里。