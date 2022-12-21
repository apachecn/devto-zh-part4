# 学习 NextJs -第 8 天

> 原文：<https://dev.to/eperedo/learning-nextjs-day-8-11>

你可以很容易地添加别名到你的页面，但是现在 404 问题会在你每次刷新页面时发生。
为了解决这个问题，你需要创建一个定制服务器，我知道！听起来很可怕，但实际上并不可怕，因为 nextjs 是一个节点 web 服务器。

这意味着将它与 express、hapijs、fastify 或任何其他节点服务器集成起来非常容易。在他们的文档中有很多如何做到这一点的例子。

我打算用 HapiJs，因为我太喜欢它了。

### 创建 HapiJs 服务器

第一件事是安装 hapijs 作为依赖项

```
npm install @hapi/hapi 
```

现在您需要创建一个文件，next 和 hapi 之间的集成就发生在这个文件中。

```
touch server.js # this create a new file server.js 
```

首先，您需要创建一个简单的 hapijs 服务器。

```
const Hapi = require("@hapi/hapi");

const server = Hapi.Server({
  host: process.env.HOST || "localhost",
  port: process.env.PORT || 3000
});

(async () => {
  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})(); 
```

请记住，这是服务器端和更具体的 nodejs 领域，所以您还不能使用花哨的 **import** 语法。那是因为我正在使用**要求**。

之后，我们使用**哈比神创建一个 hapi 服务器。Server** 方法，你可以传递很多选项给它，但是现在主机和端口就足够了。两者都有默认值 **localhost:3000** ，但是您可以使用环境变量来更改它。然后在一个 IIFE 中我们调用 start 方法，这是一个承诺，所以我们可以使用 await 关键字，然后用服务器信息向终端打印一条好消息。

### 整合 NextJs 和 HapiJs

由于 NextJs 是另一个节点 web 服务器，所以创建它几乎与 hapijs 相同。首先是 require，然后是返回服务器实例的方法，最后是访问 nextjs 内部的函数。

```
// The Require!
const next = require("next");
const Hapi = require("@hapi/hapi");

// the method that return a nextjs instance
// the dev property will start next in dev mode
// in production you want to pass it a falsy value.
const app = next({ dev: true });

const server = Hapi.Server({
  host: process.env.HOST || "localhost",
  port: process.env.PORT || 3000
});

(async () => {
  // We need to wait to next to be ready
  // then start the hapi server.
  await app.prepare();

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})(); 
```

酷，但是我们的 HapiJs 服务器不知道如何渲染 react 组件。我们如何将这种能力赋予我们的 HapiJs 服务器？nextjs 团队领先一步，创建了一个帮助文件来使集成更加容易。

你可以[在这里](https://github.com/zeit/next.js/blob/master/examples/custom-server-hapi/next-wrapper.js)下载。
我们将把它添加到项目中，命名为 **nextWrapper.js** ，需要它并创建几条新路线。

现在我们在 **pages** 文件夹中有两个文件:Index 和 About。但是我们可能有更多的文件，那么我们如何告诉哈比神获取这个文件夹中的所有文件，渲染组件，并将 html 返回给客户端呢？我们将创建一个通用的路由，它将基本上匹配到我们的 hapijs 服务器的任何可能的请求，并用 **nextWrapper.js** 文件中的一个方法处理这个路由。

```
// other lines of code omitted
// for better reading purposes
const { defaultHandlerWrapper } = require("./nextWrapper");

(async () => {
  await app.prepare();

  // We use regex to tell hapi to catch
  // all the request and process them using the
  // defaultHandlerWrapper method.
  server.route({
    method: "GET",
    path: "/{p*}",
    handler: defaultHandlerWrapper(app)
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})(); 
```

**defaultHandlerWrapper** 方法负责处理对我们的 hapijs 服务器的所有请求，并在默认情况下实现 nextjs 的功能。

Nextjs 在一个 **_next** 文件夹中创建了很多 courtains 后面的东西。这意味着我们需要 hapijs 服务器中的另一条路径。在这种情况下，我们将使用 **nextWrapper.js** 文件的 **nextHandlerWrapper** 方法。

```
// other lines of code omitted
// for better reading purposes
const { defaultHandlerWrapper, nextHandlerWrapper } = require("./nextWrapper");

(async () => {
  await app.prepare();

  server.route({
    method: "GET",
    path: "/_next/{p*}",
    handler: nextHandlerWrapper(app)
  });

  server.route({
    method: "GET",
    path: "/{p*}",
    handler: defaultHandlerWrapper(app)
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})(); 
```

现在，我们不能再使用 **dev** 脚本，因为它将打开下一个服务器，我们希望启动与 next 集成的新 hapijs 服务器，所以让我们更改该脚本。

```
{  "scripts":  {  "dev":  "node server.js"  }  } 
```

如果你在你的终端键入 **npm run dev** 一切都是一样的！是的，你仍然会在 about 页面看到 404 错误。
让我们在 hapijs 服务器中创建一条路由，在这种情况下，该路由需要以下格式:

```
/users/{userId}/about 
```

每次这个 url 得到一个请求时，它应该指向**页面**文件夹中的文件 **about.js** 并执行下一个魔法(读取服务器上的组件，呈现它并将响应返回给客户端)。
它与前面的路线非常相似，但是现在我们在 **nextWrapper.js** 文件中使用第三种方法: **pathWrapper** 。

```
// other lines of code omitted
// for better reading purposes
const {
  defaultHandlerWrapper,
  nextHandlerWrapper,
  pathWrapper
} = require("./nextWrapper");

(async () => {
  await app.prepare();

  server.route({
    method: "GET",
    // this path is the same we put in the "as" prop
    // of the Link Component
    path: "/users/{userId}/about",
    // This method get the request from the client and return
    // the result of the about.js file
    handler: pathWrapper(app, "/about")
  });

  server.route({
    method: "GET",
    path: "/_next/{p*}",
    handler: nextHandlerWrapper(app)
  });

  server.route({
    method: "GET",
    path: "/{p*}",
    handler: defaultHandlerWrapper(app)
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})(); 
```

好的，我们需要再次启动我们的服务器

```
npm run dev 
```

如果您选择了一个用户，您仍然可以在浏览器中看到您的 URL“/users/{ id }/about ”,但是如果您刷新该页面，您将不再看到 404 页面！