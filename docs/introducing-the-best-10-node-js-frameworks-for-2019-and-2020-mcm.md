# 介绍 2019 年和 2020 年最佳的 10 个 Node.js 框架

> 原文：<https://dev.to/santypk4/introducing-the-best-10-node-js-frameworks-for-2019-and-2020-mcm>

**最初发表于[softwareontheroad.com](https://softwareontheroad.com/nodejs-frameworks/)T3】**

我已经厌倦了阅读那些基于偏见或赞助声称什么是最好的 node.js 框架的文章*(是的，那是一个东西)*

以下是按日下载量排名的 node.js 框架，数据取自 npmjs.com 自己*(抱歉纱线)*。

[https://www.youtube.com/embed/P0Xk8UhawEQ](https://www.youtube.com/embed/P0Xk8UhawEQ)

# 什么是 node.js 框架？

# 如何为我的应用选择一个 node.js 框架？

你必须主要考虑两件事:

1.  框架的可伸缩性和健壮性

2.  如果开发过程是你觉得舒服的工作。

不考虑可伸缩性和健壮性，每个 node.js web 框架都构建在`http`模块之上。

其中一些框架增加了太多…这对服务器的吞吐量产生了巨大的影响。

在我看来，当您正在开发的服务在业务逻辑上很小，但需要高度可伸缩性时，使用像 Express.js 或 Fastify.js 这样的准系统框架是最好的。

另一方面，如果你正在开发一个中等大小的应用程序，最好使用一个框架来帮助你有一个清晰的结构，比如 next.js 或者 loopback。

这个问题没有简单的答案，你最好先看看如何在这个列表中的每个框架上声明 API 路由，然后自己决定。

# 10。阿多尼斯

[*Adonis.js*](https://adonisjs.com/) 是一个 MVC(模型-视图-控制器)node.js 框架，能够构建具有 JWT 认证和数据库访问的 API Rest。

## 这个框架是关于什么的？

好消息是 Adonis.js framework 附带了一个 CLI 来创建应用程序的引导。

```
$ npm i -g @adonisjs/cli
$ adonis new adonis-tasks
$ adonis serve --dev 
```

Enter fullscreen mode Exit fullscreen mode

典型的 Adonis 应用程序有一个 MVC 结构，这样你就不用浪费时间去考虑如何构建你的 web 服务器。

一些用 adonis 构建的应用可以[在这里找到。](https://madewithadonisjs.com/)

# 👉获取更高级的节点。JS 开发文章

加入其他 2000 多名精通 node.js 的开发人员的行列，获取文章更新。

[![Email List Form](img/ab6595eef5c66adc798d313eced1d083.png)](https://softwareontheroad.us20.list-manage.com/subscribe/post?u=337d8675485234c707e63777d&id=14f1331817)

# 9。羽毛

[*Feather.js*](https://feathersjs.com/) 是一个 node.js 框架，承诺成为现代应用程序的 REST 和实时 API 层。

## 看看有什么能耐！！

多亏了 socket.io 插件
，这就是你设置 API REST +实时 WebSockets 连接所需的所有代码

```
const feathers = require('@feathersjs/feathers');
const express = require('@feathersjs/express');
const socketio = require('@feathersjs/socketio');

const memory = require('feathers-memory');

// Creates an Express compatible Feathers application
const app = express(feathers());

// Parse HTTP JSON bodies
app.use(express.json());
// Parse URL-encoded params
app.use(express.urlencoded({ extended: true }));
// Add REST API support
app.configure(express.rest());
// Configure Socket.io real-time APIs
app.configure(socketio());
// Register a messages service with pagination
app.use('/messages', memory({
  paginate: {
    default: 10,
    max: 25
  }
}));
// Register a nicer error handler than the default Express one
app.use(express.errorHandler());

// Add any new real-time connection to the `everybody` channel
app.on('connection', connection => app.channel('everybody').join(connection));
// Publish all events to the `everybody` channel
app.publish(data => app.channel('everybody'));

// Start the server
app.listen(3030).on('listening', () =>
  console.log('Feathers server listening on localhost:3030')
); 
```

Enter fullscreen mode Exit fullscreen mode

很可爱吧？

下面是一些用 feathers.js 构建的应用

# 8。帆

*[sails . js](https://sailsjs.com/)Ye ' olde node . js 框架*

经过 7 年的发展，这是一个久经考验的 node.js web 框架，你一定要看看！

## 见其行动

Sails.js 附带了一个 CLI 工具，可以帮助你通过 4 个步骤开始

```
$ npm install sails -g
$ sails new test-project
$ cd test-project
$ sails lift 
```

Enter fullscreen mode Exit fullscreen mode

# 7。回路

背靠 IBM， [Loopback.io](https://loopback.io) 是一个企业级 node.js 框架，被 GoDaddy、Symantec、IBM 本身等公司使用。

他们甚至提供 18 个月的长期支持(LTS )!

这个框架附带了一个 CLI 工具来搭建 node.js 服务器

```
$ npm i -g @loopback/cli 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个项目

```
$ lb4 app 
```

Enter fullscreen mode Exit fullscreen mode

下面是 API 路由和控制器的样子:

```
import {get} from '@loopback/rest';

export class HelloController {
  @get('/hello')
  hello(): string {
    return 'Hello world!';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 6。Fastify

[Fastify.io](https://www.fastify.io) 是一个 node.js 框架，旨在取代 express.js [，性能提升 65%](https://www.fastify.io/benchmarks/)。

## 显示代码

```
// Require the framework and instantiate it
const fastify = require('fastify')({
  logger: true
})

// Declare a route
fastify.get('/', (request, reply) => {
  reply.send({ hello: 'world' })
})

// Run the server!
fastify.listen(3000, (err, address) => {
  if (err) throw err
  fastify.log.info(`server listening on ${address}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

我喜欢 Fastify.js 的简单性和对 Express.js 的回忆，如果您的服务器存在性能问题，它无疑是一个不错的框架。

# 5。重新定义

[Restify](http://restify.com/) 号称 Node.js Web 框架的未来。

这一框架被 NPM、网飞、Pinterest 和 Napster 用于制作。

## 代码示例

设置一个 Restify.js 服务器就是这么简单

```
const restify = require('restify');

function respond(req, res, next) {
  res.send('hello ' + req.params.name);
  next();
}

const server = restify.createServer();
server.get('/hello/:name', respond);
server.head('/hello/:name', respond);

server.listen(8080, function() {
  console.log('%s listening at %s', server.name, server.url);
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 👉获取更高级的节点。JS 开发文章

加入其他 2000 多名精通 node.js 的开发人员的行列，获取文章更新。

[![Email List Form](img/ab6595eef5c66adc798d313eced1d083.png)](https://softwareontheroad.us20.list-manage.com/subscribe/post?u=337d8675485234c707e63777d&id=14f1331817)

# 4。嵌套. js

一个相对较新的 node.js 框架， [Nest.js](https://nestjs.com) 具有与 Angular.io 相似的架构，所以如果你熟悉那个前端框架，你会发现这个框架也很容易开发。

## 举例

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.setViewEngine('hbs');
  await app.listen(3000);
}
bootstrap(); 
```

Enter fullscreen mode Exit fullscreen mode

# 3。哈皮

作为三大 node.js 框架之一， [hapi.js](https://hapi.dev) 拥有一个由库和插件组成的生态系统，使得该框架具有高度的可定制性。

虽然我从未在生产中使用过 hapi.js，但我已经使用它的验证库 Joi.js 很多年了。

## 创建服务器

hapi.js 服务器看起来像这样

```
const Hapi = require('@hapi/hapi');

const init = async () => {

  const server = Hapi.server({
      port: 3000,
      host: 'localhost'
  });

  await server.start();
  console.log('Server running on %s', server.info.uri);
};

init(); 
```

Enter fullscreen mode Exit fullscreen mode

# 2\. Koa

[Koa](https://koajs.com) 是 Express.js 背后的团队设计的一个 web 框架，最著名、使用最多的 node.js 框架。

Koa 的目标是成为比 express.js 更小、更有表现力、更健壮的 web 应用程序和 API 基础。

通过利用生成器，Koa 允许您放弃回调并大大提高错误处理能力。

Koa 没有在核心中捆绑任何中间件，而是提供了一套优雅的方法，使得编写服务器变得快速而有趣。

## 举例

```
const Koa = require('koa'); 
const app = new Koa(); 
app.use(async ctx => { 
  ctx.body = 'Hello World'; 
}); 
app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

# 1。表达

Express.js 无疑是 node.js 框架之王，到 2019 年底将达到令人难以置信的 200 万次每日下载。

尽管是这样一个老框架，Express.js 仍由社区积极维护，并被 User、Mulesoft、IBM 等大公司使用。

## 举例

只需将它添加到 node.js 项目中

`$ npm install express`

然后声明一些 API 路由

```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`)) 
```

Enter fullscreen mode Exit fullscreen mode

这就是你开始使用它所需要的一切！

# 结论

有大量的 node.js 框架，你能做的最好的事情就是去尝试所有的框架，直到你找到适合你需求的。

就我个人而言，我更喜欢 Express.js，因为通过这 6 年的 node.js 开发，我建立了良好架构模式的强大知识，所有这些都是基于反复试验。

但这并不意味着你必须做同样的事情，这里是一个好的 express.js 框架项目的所有秘密。

[![santypk4 image](img/3da502abc622980763f568f315d67060.png)](/santypk4) [## 防弹 node.js 项目架构🛡️

### Sam Apr 18 ' 1911 分钟读取

#node #javascript #webdev #tutorial](/santypk4/bulletproof-node-js-project-architecture-4epf)

## 现在告诉我，你最喜欢的 node.js 框架是什么？

给我发个推文到 [@santypk4](https://twitter.com/santypk4) ，加油！我想知道人民在用什么，我不想落后！

# 👉获取更高级的节点。JS 开发文章

加入其他 2000 多名精通 node.js 的开发人员的行列，获取文章更新。

[![Email List Form](img/ab6595eef5c66adc798d313eced1d083.png)](https://softwareontheroad.us20.list-manage.com/subscribe/post?u=337d8675485234c707e63777d&id=14f1331817)