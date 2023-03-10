# 用 VS 代码 Dockerize 一个 Node.js 应用

> 原文：<https://dev.to/vguleaev/dockerize-a-node-js-app-with-vs-code-29c4>

你好亲爱的程序员，欢迎来到我的技术文章系列，献给 *Node.js* 和 *Docker* 。希望你喜欢！

## 问题:

你以前从未使用过 Docker，你是一名 JavaScript 开发人员。您希望了解如何将容器与节点一起使用。我们走吧！

## 1。设置

在本教程中，我将使用我最喜欢的编辑器 Visual Studio 代码。你还需要安装 [Nodejs](https://nodejs.org/en/download/) 和 Docker。在我的例子中，我使用的是 Windows，所以我从这里得到了 Windows 的 [Docker。](https://hub.docker.com/editions/community/docker-ce-desktop-windows)

下一个命令创建一个名为 test-node-app 的新文件夹，然后创建 npm 包文件并在 VS 代码中打开文件夹。

```
mkdir test-node-app && cd test-node-app && npm init -y && code . 
```

Enter fullscreen mode Exit fullscreen mode

另外，请安装 express 来创建服务器。

```
npm i express 
```

Enter fullscreen mode Exit fullscreen mode

## 2。创建基本节点应用程序

打开 package.json 文件，修改如下:

```
{
 "name": "test-node-app",
 "version": "1.0.0",
 "description": "",
 "main": "server.js",
 "scripts": {
   "start": "node server.js",
   "test": "echo \"Error: no test specified\" && exit 1"
 },
 "keywords": [],
 "author": "",
 "license": "ISC",
 "dependencies": {
   "express": "^4.17.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在添加名为 server.js 的新文件: