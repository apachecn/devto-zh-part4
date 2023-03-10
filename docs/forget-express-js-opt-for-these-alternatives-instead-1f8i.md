# 忘记 Express.js 吧——选择这些替代品

> 原文：<https://dev.to/bnevilleoneill/forget-express-js-opt-for-these-alternatives-instead-1f8i>

[![](img/944e72feb53f535ace4dbc1a1d908dc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zaal4o15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rhv2aewtreb2dmwbv6n9.jpeg)

在构建 HTTP 服务器时，Node.js 提供了一些强大的原语。默认情况下，每当服务器收到 HTTP 请求时，就会运行一个函数。众所周知，解析包含 JSON 主体的传入 POST 请求的服务器示例看起来有点像这样:

```
const http = require('http');

const server = http.createServer((req, res) => {
  // This function is called once the headers have been received
  res.setHeader('Content-Type', 'application/json');

  if (req.method !== 'POST' || req.url !== '/user') {
    res.statusCode = 405;
    res.end('{"error":"METHOD_NOT_ALLOWED"}');
    return;
  }

  let body = '';

  req.on('data', (data) => {
    // This function is called as chunks of body are received
    body += data;
  });

  req.on('end', () => {
    // This function is called once the body has been fully received
    let parsed;

    try {
      parsed = JSON.parse(body);
    } catch (e) {
      res.statusCode = 400;
      res.end('{"error":"CANNOT_PARSE"}');
    }

    res.end(JSON.stringify({
      error: false,
      username: parsed.username
    }));
  });
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
}); 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Node.js 允许我们在收到任何请求时运行一个函数。没有基于路径的内置路由器。Node.js 执行一些基本的解析——例如，解析传入的 HTTP 消息并提取不同的组件，如路径、头对、编码(Gzip 和 SSL)等。

然而，对更高级功能的需求意味着我们通常不得不使用 web 框架。例如，如果收到一个`multipart/form-data`或`application/x-www-form-urlencoded`请求，我们需要使用一个模块来为我们解码内容。如果我们想简单地基于模式匹配和 HTTP 方法路由请求，我们将需要一个模块——或者，通常，一个完整的 web 框架——来为我们处理这个问题。

这就是像 Express.js 这样的工具发挥作用的地方。

## 遇见快递. js

Express.js 很早就成为使用 Node.js 构建 web 应用程序的首选框架。它抓住了许多开发人员的痛处:它为路由 HTTP 请求提供了一个很好的语法，为构建中间件提供了一个标准化的接口，而且它使用了核心 Node.js APIs 和大多数 npm 生态系统所采用的熟悉的回调模式。

Express.js 变得如此流行，以至于它几乎无处不在地与 Node.js 联系在一起——就像当我们读到 Ruby 语言时，我们已经想到了框架 Rails。事实上，Express.js 和 Node.js 都是流行的 MEAN 和 MERN 堆栈首字母缩略词的成员。

让我们来看看当我们把 Express.js 带入图片时，我们之前的例子可能是什么样子的:

```
const express = require('express');
const app = express();

app.post('/user', (req, res) => {
  // This function is called once the headers have been received

  let body = '';

  req.on('data', (data) => {
    // This function is called as chunks of body are received
    body += data;
  });

  req.on('end', () => {
    // This function is called once the body has been fully received
    let parsed;

    try {
      parsed = JSON.parse(body);
    } catch (e) {
      res.statusCode = 400;
      res.json({
        error: 'CANNOT_PARSE'
      });
    }

    res.json({
      error: false,
      username: parsed.username
    });
  });

});

app.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们看到情况有所好转。我们可以通过使用`app.post('/user')`来具体说明我们想要匹配的方法和路径。这比在处理程序中编写一个大的分支语句要简单得多。

我们也被赋予了一些其他的细节。考虑一下`res.json({})`方法:这不仅将一个对象序列化为它的 JSON 等价物，而且还为我们设置了适当的`Content-Type`头！

然而，Express.js 仍然给了我们使用内置`http`模块时得到的相同范式；例如，我们仍然在调用`req`和`res`对象上的方法。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 一个理想的例子

让我们后退一步，看看 HTTP 服务器的理想示例是什么样的。路由是可取的，Express.js 具有强大的路由语法(例如，它支持动态路由模式)。然而，在控制器函数中运行的代码是我们真正想要清理的地方。

在上面的例子中，我们用异步代码做了很多工作。请求对象是一个事件发射器，它发出我们关心的两个事件，即`data`和`end`。但是，实际上，我们通常只是希望能够将 HTTP 请求转换成 JSON 对象，以便于从中提取值。

此外，我们还得到一个请求(`req`)和一个响应(`res`)对象。`req`对象是有意义的——它包含了关于我们正在接收的请求的信息。但是`res`真的有那么多意义吗？我们只想提供一个来自控制器函数的结果作为回复。

对于同步函数，从函数调用中接收结果很简单:只需返回值。如果我们利用`async`函数，我们可以做同样的事情。通过返回对一个`async`函数的调用，控制器函数可以解析一个值，该值最终表示我们希望消费者接收的响应。

让我们看一个这样的例子:

```
const server = someCoolFramework();

server.post('/user', async (req) => {
  let parsed;

  try {
    parsed = await req.requestBodyJson();
  } catch (e) {
    return [400, {
      error: 'CANNOT_PARSE'
    }];
  }

  return {
    error: false,
    username: parsed.username
  };
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们这个理想化的例子中有一些概念。首先，我们维护 Express.js 使用的现有路由器语法，因为它相当可靠。其次，我们的`req`对象提供了一个助手，用于将传入的请求转换成 JSON。

第三个特性是，我们能够通过简单地返回结果来提供响应的表示。由于 JavaScript 不支持元组，我们实际上是通过使用数组来重新创建一个元组。因此，在这个虚构的例子中，返回的字符串可以作为主体直接发送给客户机，返回的数组可以用于表示状态代码和主体(可能还有第三个参数用于元数据，如头)，返回的对象可以转换成它的 JSON 表示。

## 改编快递. js

现在，使用一组中间件，用 Express.js 实际上可以重现一些这种行为。

`express-async-handler` npm 模块提供了一个包装函数，它可以插入并允许`async`控制器函数与 Express.js `app.use` API 很好地交互。不幸的是，这需要开发人员手动包装每个控制器功能:

```
const asyncHandler = require('express-async-handler')

app.post('/user', asyncHandler(async (req, res, next) => {
  const bar = await foo.findAll();
  res.send(bar);
})) 
```

Enter fullscreen mode Exit fullscreen mode

响应元组展开也可以由中间件来处理。这种中间件需要在控制器代码运行后运行，并用 Express.js 期望的表示替换数组。

承诺请求主体流解析的能力也可以以通用的方式构建:

```
app.use((req, res, next) => {
  req.bodyToJson = requestBodyJson(req);
  next();
});

function requestBodyJson(req) {
  return new Promise((resolve, reject) => {
    let body = '';

    req.on('data', (data) => {
      // This function is called as chunks of body are received
      body += data;
    });

    req.on('end', () => {
      // This function is called once the body has been fully received
      let parsed;

      try {
        parsed = JSON.parse(body);
      } catch (e) {
        reject(e);
        return;
      }

      resolve(parsed);
    });
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

有了上面的代码，我们就可以等待使用 Express.js 进行解析了(实际上还有其他任何给定 HTTP `Request`对象实例的情况):

```
// When using the Express.js middleware:
const parsed = await req.bodyToJson();

// Using the function generically:
const parsed = await requestBodyJson(req); 
```

Enter fullscreen mode Exit fullscreen mode

## 使用另一个框架

的确，我们可以使用 Express.js 来复制这些期望的模式，但是也有一些框架是在支持 promises 和 async/await 范式的基础上构建的。让我们看看使用不同的 web 服务器框架编写的示例控制器是什么样子的。

### 快速化

顾名思义，Fastify 是一个非常快速的 Node.js web 框架。尽管它的主要目标是速度，但它实际上很好地实现了我们理想的控制器语法。

这个例子如此简洁，几乎让人感觉像是作弊:

```
const fastify = require('fastify');
const app = fastify();

app.post('/user', async (req, reply) => {
  return {
    error: false,
    username: req.body.username
  };
});

app.listen(3000).then(() => {
  console.log('Server running at http://localhost:3000/');
}); 
```

Enter fullscreen mode Exit fullscreen mode

Fastify 不仅支持将`async`函数用作控制器代码，而且如果`Content-Type`头表明主体是 JSON，它还会自动将传入的请求解析为 JSON。这就是示例代码如此之小的原因。

这也意味着当解析失败时，我们可以依靠 Fastify 来响应一个正常的错误。例如，当客户端向 Fastify 发送无效的 JSON 时，响应看起来会像这样:

```
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "Unexpected string in JSON at position 19"
} 
```

Enter fullscreen mode Exit fullscreen mode

### Koa

[Koa](https://koajs.com/) 是 Express.js 的精神继承者，由一些最初的 Express.js 作者编写。它确实支持户外功能，但它没有自带路由器。我们可以利用`koa-router`来提供路由。

下面是我们的示例控制器在 Koa 中的样子:

```
const Koa = require('koa');
const Router = require('koa-router');
const app = new Koa();
const router = new Router();

router.post('/user', async (ctx) => {
  try {
    const parsed = await requestBodyJson(ctx.req);
    ctx.body = {
      error: false,
      username: parsed.username
    };
  } catch (e) {
    ctx.status = 400;
    ctx.body = {
      error: 'CANNOT_PARSE'
    };
  }
});

app.use(router.routes());
app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

这个 Koa 例子不像 Fastify 版本那样简洁。它不执行自动 JSON 解析，但是我们能够重用我们之前创建的`requestBodyJson()`方法。它也不使用从我们的控制器返回/解析的值，而是通过使用附加到`ctx`参数的数据来工作。

## 外卖

当 Node.js 还处于起步阶段时，Express.js 成为构建 web 应用程序的明显选择。Express.js 的目标是成为一个遵循回调范例的方便的 web 服务器。它实现了这个目标，产品现在基本上已经完成了。

然而，随着 JavaScript 生态系统的成熟，我们获得了新的语言工具和语法。从那以后，出现了几十个甚至几百个框架，其中许多都采用了这些新的语言特性。

如果您发现自己正在开发一个用 Node.js 编写的新项目，该项目充当 web 服务器，我鼓励您考虑新的竞争者，如 Koa 和 Fastify，而不是默认使用熟悉的 Express.js。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://logrocket.com/signup/)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[忘记 express . js——选择这些替代方案](https://blog.logrocket.com/forget-express-js-opt-for-these-alternatives-instead/)首先出现在[博客](https://blog.logrocket.com)上。