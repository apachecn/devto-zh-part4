# 用 Jest 和 Supertest 进行端点测试

> 原文：<https://dev.to/zellwk/endpoint-testing-with-jest-and-supertest-27gf>

我最近在玩测试。我尝试做的一件事是测试我的 Express 应用程序的端点。

设置测试是最难的部分。写测试的人实际上不会教你他们是如何设置的。我找不到任何有用的信息，我不得不试着弄清楚。

所以今天，我想分享我为自己创建的设置。希望这能在您创建自己的测试时帮助您。

首先，我们来谈谈堆栈。

## 堆栈

*   我用 Express 创建了我的应用程序。
*   我用 Mongoose 连接到 MongoDB
*   我使用 Jest 作为我的测试框架。

您可能已经预料到了 Express 和 Mongoose，因为其他人似乎都在使用这两个框架。我也用过。

但是为什么是 Jest 而不是其他测试框架呢？

## 为什么是

我不喜欢脸书，所以我不想尝试脸书团队创造的任何东西。我知道这听起来很傻，但这是事实。

在 Jest 之前，我尝试了各种测试框架。我尝试了踢踏舞、磁带、摩卡、茉莉和 AVA。每个测试框架都有自己的优点和缺点。我差点就选择了 AVA，但我没有选择 AVA，因为我发现这很难安排。最终，我尝试了 Jest out，因为 Kent C. Dodds 推荐了它。

试过之后就爱上了 Jest。我喜欢它，因为:

1.  这很容易设置
2.  [手表模式](https://egghead.io/lessons/javascript-use-jest-s-interactive-watch-mode)太神奇了
3.  当你做某事时，它毫无困难地出现了(这是 AVA 的一个缺点)。

## 设置笑话

首先，你需要安装 Jest。

```
npm install jest --save-dev 
```

接下来，您想要添加测试脚本到您的`package.json`文件中。它有助于添加`test`和`test:watch`脚本(分别用于一次性测试和观察模式)。

```
"scripts": {
  "test": "jest",
  "test:watch": "jest --watch"
}, 
```

您可以选择以下列格式之一来编写您的测试文件。Jest 会自动帮你捡起来。

1.  `__tests__`文件夹中的`js`文件
2.  以`test.js`命名的文件(如`user.test.js`
3.  以`spec.js`命名的文件(如`user.spec.js`

你可以随意摆放你的文件。当我测试端点时，我把测试文件和我的端点放在一起。我发现这更容易管理。

```
- routes
  |- users/
    |- index.js
    |- users.test.js 
```

## 编写你的第一个测试

Jest 在每个测试文件中都为你包含了`describe`、`it`和`expect`。你不用`require`他们。

*   让您将许多测试打包在一起。(它用于组织您的测试)。
*   让您运行测试。
*   让您执行断言。如果所有断言都通过了，测试就通过了。

这里有一个测试失败的例子。在这个例子中，我`expect`认为`1`应该严格等于`2`。从`1 !== 2`开始，测试失败。

```
// This test fails because 1 !== 2
it("Testing to see if Jest works", () => {
  expect(1).toBe(2);
}); 
```

如果你运行 Jest，你会从 Jest 看到一个失败的消息。

```
npm run test:watch 
```

[![Output from Terminal. Test fails.](img/ab6f2009c06a5b00ae603ca2cdea49a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tZfRnyh3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2019/endpoint-testing/test-fail.png)

期待`1 === 1`就能让测试通过。

```
// This passes because 1 === 1
it("Testing to see if Jest works", () => {
  expect(1).toBe(1);
}); 
```

[![Output from Terminal. Test successful.](img/ec8fe633811029e6140e3c97624b849d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gw72Fbxk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2019/endpoint-testing/test-pass.png)

这是最基本的测试。这一点用都没有，因为我们还没有测试任何真实的东西。

## 异步测试

您需要发送一个请求来测试一个端点。请求是异步的，这意味着您必须能够进行异步测试。

这很容易理解。有两个步骤:

1.  添加`async`关键字
2.  当你完成测试后打电话给`done`

它可能是这样的:

```
it("Async test", async done => {
  // Do your async tests here

  done();
}); 
```

注意:[如果你不知道如何使用 JavaScript，这里有一篇关于 Async/await 的文章](https://zellwk.com/blog/async-await)。

## 测试端点

您可以使用 Supertest 来测试端点。首先，您需要安装 Supertest。

```
npm install supertest --save-dev 
```

在测试端点之前，您需要设置服务器，以便 Supertest 可以在您的测试中使用它。

大部分教程教你`listen`到服务器文件里的 Express app，像这样:

```
const express = require("express");
const app = express();

// Middlewares...
// Routes...

app.listen(3000); 
```

这不起作用，因为它开始监听一个端口。如果你试图写很多测试文件，你会得到一个错误，说“端口正在使用”。

您想要允许每个测试文件自己启动一个服务器。要做到这一点，你需要在不监听的情况下导出`app`。

```
// server.js
const express = require("express");
const app = express();

// Middlewares...
// Routes...

module.exports = app; 
```

出于开发或生产的目的，您可以像平常一样在一个不同的文件(如`start.js`)中收听您的`app`。

```
// start.js
const app = require("./server.js");
app.listen(3000); 
```

### 使用 Supertest

要使用 Supertest，您需要在测试文件中包含您的应用程序和 supertest。

```
const app = require("./server"); // Link to your server file
const supertest = require("supertest");
const request = supertest(app); 
```

一旦这样做了，您就能够发送 get、POST、PUT、PATCH 和 DELETE 请求。在发送请求之前，我们需要有一个端点。假设我们有一个`/test`端点。

```
app.get("/test", async (req, res) => {
  res.json({ message: "pass!" });
}); 
```

要向`/test`发送 GET 请求，可以使用 Supertest 中的`.get`方法。

```
it("Gets the test endpoint", async done => {
  // Sends GET Request to /test endpoint
  const res = await request.get("/test");

  // ...
  done();
}); 
```

Supertest 给你一个来自端点的响应。您可以测试 HTTP 状态和正文(无论您通过`res.json`发送什么)，如下所示:

```
it("gets the test endpoint", async done => {
  const response = await request.get("/test");

  expect(response.status).toBe(200);
  expect(response.body.message).toBe("pass!");
  done();
}); 
```

[![First endpoint test passes.](img/9846c2c841f64fec6e296bbe68d5fcab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lrCtIMGE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2019/endpoint-testing/test-endpoint-pass.png)

如果你想了解更多关于 Supertest 的信息，你可以阅读它的文档[这里](https://github.com/visionmedia/supertest)。

在下一篇文章中，我将向您展示如何执行 POST 请求，以及如何在您的测试文件中连接到 Mongoose。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。