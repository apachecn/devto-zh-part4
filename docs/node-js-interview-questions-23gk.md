# Node.js 面试问题

> 原文：<https://dev.to/fullstacktuts/node-js-interview-questions-23gk>

[![Node.js Interviews Questions](img/0fe5ff16798fa3f4404a014d70d8c0b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dn0-3xI6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jcuipz3i1ovr1fdg7na.png)

### 问:Node.js 是什么？

#### Node.js 是一个开源、跨平台的 JavaScript 运行时环境，用于在服务器端执行 JavaScript 代码。

Ryan Dahl 想出了一个绝妙的主意，他认为在服务器上执行浏览器手段之外的 JavaScript 会很棒，所以他拿了 Google 的 JavaScript V8 引擎，嵌入了 C++程序，并将其命名为 Node。

#### 最流行的 JavaScript 引擎:

谷歌 Chrome - V8 //最快的 JavaScript 引擎
Mozilla FireFox-spider monkey
微软 Edge - Chakra

### 问:Node.js 的关键特性是什么？

##### 让我们来看看 Node.js 的一些关键特性

### 异步和事件驱动-

Node.js 库的所有 API 都是异步的，即非阻塞 I/O。

意味着 API 调用不等待结果，也不阻塞其他调用。在它完成执行后，它将运行一个回调来通知它的完成。

### 很快——

Node.js 使用谷歌 Chrome 的用 C++编写的 V8 JavaScript 运行时引擎，该引擎将 JavaScript 代码编译成机器代码，使其速度更快。

它用于客户端(Google Chrome)和服务器端(Node.js) JavaScript 应用程序。

JavaScript 引擎:它是一个将 JavaScript 的代码转换成底层或机器码的程序。

### 单线程但高度可扩展-

Node.js 是单线程的，它在后台(在 Node.js 的引擎盖下通过 libuv 使用多个线程)使用多个线程执行异步代码。

所有 Node.js 应用程序都使用“单线程事件循环模型”架构来处理多个并发客户端。

事件循环机制有助于服务器以非阻塞的方式做出响应，从而使服务器具有高度的可伸缩性，而传统服务器创建有限的线程来处理请求。

### Node.js 库使用 JavaScript

从开发者的角度来看，这是 Node.js 的另一个重要方面。大多数开发人员已经精通 JavaScript。因此，对于懂 JavaScript 的开发人员来说，Node.js 的开发变得更加容易。

### NPM(节点包经理)—

NPM 代表节点包管理器，它允许我们为 Node.js 应用程序安装各种包。

### 无缓冲-

Node.js 应用程序从不缓冲任何数据。他们只是以块的形式输出数据。

### 社区-

Node.js 框架有一个活跃的社区——活跃的社区总是让框架随着 web 开发的最新趋势而更新。

### 问:Node.js 中的模块是什么？

模块是封装在单个单元中的一组功能或 javascript 库，可以在整个 Node.js 应用程序中重用。

每个 Node.js 模块都有自己的上下文

### node . js 中模块的类型？

##### 核心(内置)模块

##### 本地(用户自定义)模块

##### 第三方模块

阅读完整文章 [Node.js 面试问题](https://www.fullstacktutorials.com/interviews/node-js-interview-questions-29.html)