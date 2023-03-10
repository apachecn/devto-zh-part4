# Rakkit:用 TypeScript 和 decorators 创建您的 GraphQL 和 REST APIs！

> 原文：<https://dev.to/owen/rakkit-create-your-graphql-and-rest-apis-with-typescript-and-decorators-cnj>

哇，又一个新框架，真的...？😅

# 所以，我们可以有一点以前的历史📜

在用纯 JavaScript 开发了许多 API 并共同维护它们之后。我决定为我未来的项目回顾我的做事方式，这是一件好事，因为在我的公司，我们决定创建一个无头 CMS，允许在选择所使用的 API([graph QL](https://graphql.org/learn/)或 REST)上有很大的自由，并按照类似于 [strapi.io](//strapi.io) 的原则运行。
这个项目有几个限制:如果我们的应用程序(代码)出现问题，我们必须能够非常积极地应对，能够为我们的客户轻松地添加功能，尽可能少地依赖外部模块，最重要的是拥有相对干净的代码并保持最大限度的干燥。
因此，经过一些研究和决策后，我们开始开发一个框架，作为 CMS 的基础，它允许我们创建 REST 和 GraphQL APIs(这两种类型的 API 可以共享相同中间件的使用),以使用 websockets 创建应用程序，并进行依赖注入。

# Rakkit 包📦

Rakkit 允许你创建一个有很多特性的后端，下面是它们的列表:

*   [GraphQL API](https://rakkit.dev/graphql/start/introduction.html)
*   [REST API](https://rakkit.dev/rest/router.html)
*   [路由(GraphQL 和 REST 的中间件)](https://rakkit.dev/rest/middlewares.html)
*   [Websocket 应用](https://rakkit.dev/ws/introduction.html)
*   [依赖注入](https://rakkit.dev/di/introduction.html)

# 节点 _ 模块恐惧症😫

我们都知道这个著名的文件，它可以累积很多依赖项...我们绝对希望避免这种情况，即使这意味着我们自己重新开发依赖项。然而，我们需要一些模块来使所有这些工作！以下是 Rakkit 针对每种包装的配方:

*   **graph QL API:**[graph QL](https://www.npmjs.com/package/graphql)， [graphql-subscription](https://www.npmjs.com/package/graphql-subscriptions)
*   **REST API:** [koa](https://koajs.com/) , [koa-router](https://www.npmjs.com/package/koa-router) , [koa-compose](https://www.npmjs.com/package/koa-compose)
*   **Websocket 应用:** [socket.io](//socket.io)

有一些依赖的好处是，如果我们以 koa 为例，我们可以使用社区为 koa 制作的模块，以便在 Rakkit 中使用它们！你可以将 Rakkit 与另一个依赖项并行使用，例如 [TypeORM](//typeorm.io) ！

# 哪里？📍

然后项目在 GitHub [这里](https://github.com/raccoonch/rakkit)可访问，文档在[那里](https://rakkit.dev)，当然你也可以安装在 [npm](https://www.npmjs.com/package/rakkit) 上。如果您有任何问题，我们都可以帮助您，只需发布一个[问题](https://github.com/RaccoonCH/Rakkit/issues)。
(GitHub 上的一颗小星星激励着我们继续朝着这个方向前进！).

# 好吧，但是长什么样？🧐

所以，它最终需要一些基础知识来理解其余的，所以我建议你检查一下 TypeScript，可能还有 decorators。
*这些只是非常简单的例子，并不是所有的都显示出来了...*

## 休息 API 🛣

REST 包在内部使用 koa，我们处理数据的方式，我们使用中间件的方式以及一般行为都与 [koa](//koajs.com) 相同。

```
import { Router, Get, Post, IContext, NextFunction } from "rakkit";
// Middlewares that are used for REST and GraphQL
import { Auth, SayHello } from "./middlewares.ts";
import { users } from "./users.ts";

@Router("user")
@UseMiddleware(Auth)
export class UserRouter {
  @Get("/")
  getAll(context: IContext) {
    // To return a result, assign the context.body value
    // Please refer to koa documentation for more informations...
    context.body = users;
  }

  @Get("/:id")
  @UseMiddleware(SayHello)
  async getOne(context: IContext, next: NextFunction) {
    // Omit variables checks here, for clarity
    const { id } = context.params; // JS destructuring
    const foundUser = users.find((usr) => usr.id === id);
    context.body = foundUser;
    await next();
  }

  @Post("/")
  addUser(context: IContext) {
    // Use koa-bodyparser to parse the body into an object (Rakkit documentation)
    const user = context.request.body
    users.push(user);
    context.body = user;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## web socket🔁

这很简单，只有两个装修工！

```
import { Websocket, On, Socket } from "rakkit";

@Websocket()
export class UserWS {
  @On("connection")
  onConnection(socket: Socket) {
    // Please refer to the socket.io documentation
    socket.emit("welcome", "welcome !");
  }

  @On("message")
  onMessage(socket: Socket, message: string) {
    socket.server.emit("new:message", message);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## GraphQL API🔥

GraphQL 是一个巨大的包，这只是一个非常简单的例子来看看它是什么样子，所以请参考 [Rakkit 文档](https://rakkit.dev)以获得更多信息。

```
import { ObjectType, Field } from "rakkit";

@ObjectType({ description: "Object representing an user" })
export class UserObjectType {
  @Field()
  id: string;

  @Field()
  email: string;

  @Field()
  username: string;

  @Field()
  activated: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以这样定义您的查询/变异/订阅:

```
import { Resolver, Query, IContext, NextFunction } from "rakkit";
// Middlewares that are used for REST and GraphQL
import { Auth, SayHello } from "./middlewares.ts";
import { users } from "./users.ts";

@Resolver()
@UseMiddleware(Auth)
export class UserResolver {
  // Precise the type, TS cannot resolve the return type when it's an array (Please refer to Rakkit the documentation)
  @Query(returns => UserObjectType)
  users(): UserObjectType[] {
    return users;
  }

  @Query()
  @UseMiddleware(SayHello)
  async user(
    @Arg("id")
    id: string,
    context: IContext,
    next: NextFunction
  ): UserObjectType? {
    return users.find((usr) => usr.id === id);
    await next(); // Go the the middleware function
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

它将它编译成一个 GraphQL 模式(你可以在你最喜欢的服务器实现中使用它，比如 [Apollo](https://www.apollographql.com/) 或者 [graphql-yoga](https://github.com/prisma/graphql-yoga) )。在 SDL，看起来是这样的:

```
"""Object  representing  an  user"""  type  UserObjectType  {  id:  String!  email:  String!  username:  String!  activated:  Bollean!  }  type  Query  {  users:  [UserObjectType]  user(id:  String!):  UserObjectType  } 
```

Enter fullscreen mode Exit fullscreen mode

## 依赖注入🤯

如果你从未听说过这个概念，它可能看起来很抽象，尤其是在 [Angular](https://angular.io/guide/dependency-injection) 中，所以我建议你事先去了解一下，以便能够理解(更多信息[在此](https://rakkit.dev/di/introduction.html))。

```
import { Service, Inject } from "rakkit";

@Service()
export class CronService {
  start() {
    // ...
  }
}

@Service()
export class UserService {
  @Inject()
  private cronService: CronService;

  constructor() {
    this.cronService.start();
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更多高级示例可用[此处](https://github.com/RaccoonCH/Rakkit/tree/master/examples)更多将在不久的将来推出！😉

瞧啊！感谢您花时间阅读这篇文章！👋