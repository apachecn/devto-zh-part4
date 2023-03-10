# Angular 和其他人，Nest.js 和 JWT

> 原文：<https://dev.to/thisdotmedia/angular-and-the-rest-nest-js-and-jwt-dja>

这是 Angular 和其他文章的最后一部分，我使用 Passport.js 和 JWT (JSON Web Token)在后端 Nest.js Web API 上实现了身份验证。此外，我在 Angular 应用程序端添加了一个新的身份验证模块，因此只有通过身份验证的用户才能登录。

在本周的第一部分中，我介绍了 Nrwl Nx Workspace 并创建了电影追踪器 monorepo workspace，其中包括一个用于后端的 Nest.js 应用程序和一个用于客户端的 Angular 应用程序。到目前为止，该应用程序已经完全实现了 CRUD 操作和电影搜索。在客户端，我们构建了一个 Angular v8 应用程序，它通过电影跟踪器服务与后端 RESTful Web API 进行通信。该应用程序显示所有电影，允许用户过滤电影，添加新电影，编辑现有电影和删除电影。

这是那篇文章的链接。
[棱角分明，其余同 Nest.js](https://dev.to/thisdotmedia/angular-and-the-rest-with-nest-js-2glo) 。

今天，让我们从在 Nest.js 应用程序中添加对护照和 JWT 认证的支持开始。

> 如果您没有阅读 Angular 的整个系列和其他内容，以下是链接:

*   [棱角分明，其余的](https://dev.to/thisdotmedia/angular-and-the-rest-4ho7)
*   [棱角分明，其余用 JWT 认证](https://dev.to/thisdotmedia/angular-and-the-rest-authentication-with-jwt-5em0)。

[Passport.js](http://www.passportjs.org/) 是 Node.js 开发中最成熟的认证中间件之一。在选择使用什么身份验证策略来验证用户身份方面，它提供了高度的灵活性。

Nest.js 提供了 [@nestjs/passport](https://github.com/nestjs/passport) 认证库，封装了 Passport.js 的功能，并与 Nest.js 依赖注入系统平滑集成。

让我们开始在电影追踪器应用程序的后端实现身份验证。

你可以在这个 GitHub [repo](https://github.com/bhaidar/movie-tracker/tree/add-authentication-server) 找到这篇文章的源代码。

## 向 Nest.js 添加认证

首先，我们开始安装一套 NPM 软件包，我们需要添加应用程序的认证。

运行以下命令安装软件包:
`yarn add @nestjs/passport passport passport-local passport-jwt bcryptjs`

另外，我将通过运行这个命令为下面的包安装相应的 Typescript 定义:
`yarn add @types/passport @types/bcryptjs -D`

`@nestjs/passport`库依赖于`passport`、`passport-local`和`passport-jwt`包，因此也需要安装它们。

Passport.js 通常作为节点中间件使用和配置。然而，在 Nest.js 中，@nestjs/passport 库提供了`AuthGuard`。这用于暗示特定控制器或控制器中的动作需要 Passport 验证。

> 要了解更多关于 Nest.js 中守卫的信息，请访问他们关于[守卫](https://docs.nestjs.com/guards)的官方文档。

因此，为了确保请求在访问控制器或动作之前被认证，你用这个保护来装饰你的控制器或动作(或者你可以[为所有控制器全局定义你的保护](https://docs.nestjs.com/guards#binding-guards))。剩下的由@nestjs/passport 和 passport 一起处理。

**第 1 步**
让我们对`ormconfig.json`文件进行修改，禁用`automatic synchronization`，并向您展示我们如何开始使用迁移来控制数据库中正在发生的变化。

将位于工作区根文件夹中的`ormconfig.json`文件的内容替换为以下内容: