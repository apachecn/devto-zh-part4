# 使用 NestJS 对 MVC 应用程序进行身份验证和会话

> 原文：<https://dev.to/nestjs/authentication-and-sessions-for-mvc-apps-with-nestjs-55a4>

John 是 NestJS 核心团队的成员，主要参与文档编制。

**注意:**你可以在 github 这里找到这篇文章[的所有源代码。](https://github.com/johnbiundo/mvc-sessions)

### [T1】简介](#intro)

在 [NestJS](https://github.com/nestjs/nest) (又名 Nest)中有很多关于构建 API 服务器的有用信息。这个用例非常适合 Nest。

也许令人惊讶的是，Nest 也非常擅长构建传统的 web 应用程序——在文档中，Nest 称之为 [MVC 应用程序。我指的是那些你可能会考虑在 ExpressJS + Handlebars、Laravel 或 Ruby on Rails 中构建的应用程序，它们具有模板驱动的服务器端页面呈现。考虑在这个用例中使用 Nest 的用户可能会发现这个领域的资源比 API 服务器用例要少一些。](https://docs.nestjs.com/techniques/mvc)

这可能部分是因为你需要构建的大多数信息，比如说，一个 ExpressJS + Handlebars 应用程序，已经被很好地覆盖并且很容易找到。这些信息中的大部分都直接适用于在 Nest 中构建这样一个应用。但是，就此打住，还是会给新来者留下一些问题，让他们自己去解决。本文试图从挑战的一个方面提供更多的信息:如何用 Nest 管理基于会话的认证。为什么这和 Nest 不一样？

完全回答这个问题可能需要一篇或多篇完整的文章。简短的回答是:如果你真的想利用 Nest，尝试学习用“Nest 方式”做事是有帮助的。Nest 不会为自己的固执己见道歉。它有一种处理应用程序架构的特定方式。如果你同意这种方法，那么全力以赴并尝试用嵌套的方式构建东西会有所帮助。这给了你惊人的生产力优势，帮助你构建[干巴巴的](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)可维护的代码，让你把大部分时间花在业务逻辑上，而不是样板文件上。

许多来自 Java 或。NET 背景，因为许多概念——如面向对象和依赖注入——是相似的。但是它应该对任何有`TypeScript`、`Angular`或任何现代 FE 或 be 框架/环境背景的人都同样适用。对于任何这样的框架，都有足够的空间用于个人选择和风格，但是遵循框架的一般模式会让你感觉像有风在背后支持你，而不是逆着潮流游泳。

### 要求

唷！说到这里，让我们更具体一点。当谈到手头的问题时，您只需要知道一些特定于 Nest 的技术，就可以快速跳出起点:

1.  如何设置一个类似[手柄](https://github.com/ericf/express-handlebars)的模板引擎来使用 Nest
2.  如何将模板渲染与嵌套控制器集成
3.  如何将[快速会话](https://github.com/expressjs/session)与 Nest 集成
4.  如何将认证系统(我们将特别使用 [Passport](http://www.passportjs.org/) )与 Nest 集成

第三和第四项是本文的重点。为了达到这个目的，我们将稍微讨论一下第 1 项和第 2 项(提供代码示例和嵌套文档的参考，可以帮助您更深入地了解)，但是我们不会在这上面花太多时间。如果你想在以后的文章中看到更多关于这些方面的内容，请在评论中告诉我。

让我们快速浏览一下我们将要构建的应用程序。
[![App](img/b36036a10ab8133e4fae4c14bf2652d9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--q90WoN6m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rk5x20yzmfmleusega2s.gif)

我们的要求很简单。用户将使用用户名和密码进行身份验证。一旦通过身份验证，服务器将使用快速会话，以便用户保持“登录状态”，直到他们选择注销。我们将设置一个受保护的路由，只有经过身份验证的用户才能访问。

### 安装

如果你以前没有安装过 Nest，先决条件很简单。你只需要一个现代化的 Node.js 环境(> =8.9 就行)和`npm`或者`yarn`。然后安装 Nest:

```
npm i -g @nestjs/cli 
```

我们首先安装所需的包，并构建我们的基本路线。

Passport 提供了一个名为 [passport-local](https://github.com/jaredhanson/passport-local) 的库，它实现了一个用户名/密码*认证策略*，这符合我们这个用例的需求。由于我们正在渲染一些基本的 HTML 页面，我们还将安装通用且流行的 [express-handlebars](https://github.com/ericf/express-handlebars) 包来使这变得更简单。为了支持会话并提供一种方便的方式在登录时给用户反馈，我们还将利用`express-session`和`connect-flash`包。

> **注意**对于**你选择的任何**护照策略(这里有许多可用的)，你总是需要`@nestjs/passport`和`passport`套餐。然后，您需要安装特定于策略的包(例如，`passport-jwt`或`passport-local`)，该包支持您正在构建的特定认证策略。

记住这些基本要求，我们现在可以开始搭建一个新的嵌套应用程序，并安装依赖项:

```
nest new mvc-sessions
cd mvc-sessions
npm install --save @nestjs/passport passport passport-local express-handlebars express-session connect-flash 
npm install --save-dev @types/express @types/express-session @types/connect-flash @types/express-handlebars 
```

### Web 界面

让我们从构建用于身份验证子系统 UI 的模板开始。按照标准的 MVC 类型项目结构，创建以下文件夹结构(即`public`文件夹及其子文件夹):

```
mvc-sessions
└───public
│   └───views
│       └───layouts 
```

现在创建下面的把手模板，并配置 Nest 使用快速把手作为视图引擎。参考[这里了解更多关于手柄模板语言](https://handlebarsjs.com/)，参考[这里了解更多关于服务器端渲染(MVC 风格)web 应用的嵌套特定技术](https://docs.nestjs.com/techniques/mvc)的背景。

#### 主要布局

在 layouts 文件夹中创建`main.hbs`，并添加以下代码。这是我们视图的最外层容器。注意`{{{ body }}}`行，这是每个视图被插入的地方。这个结构允许我们设置全局样式。在这种情况下，我们利用谷歌广泛使用的 [material design lite](https://github.com/google/material-design-lite) 组件库来设计我们的最小 UI。所有这些依赖关系都在我们布局的`<head>`部分处理。

```
<!-- public/views/layouts/main.hbs -->
<!DOCTYPE html>
<html>
  <head>
    <script src="https://code.getmdl.io/1.3.0/material.min.js"></script>
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.3.0/material.indigo-pink.min.css">
    <style>
      .mdl-layout__content {
        padding: 24px;
        flex: none;
      }
      .mdl-textfield__error {
        visibility: visible;
        padding: 5px;
      }
      .mdl-card {
        padding-bottom: 10px;
        min-width: 500px;
      }
    </style>
  </head>
  <body>
    {{{ body }}}
  </body>
</html> 
```

#### 首页

在`views`文件夹中创建`home.hbs`，并添加以下代码。这是用户认证后登陆的页面。

```
<!-- public/views/home.hbs -->
<div class="mdl-layout mdl-js-layout mdl-color--grey-100">
  <main class="mdl-layout__content">
    <div class="mdl-card mdl-shadow--6dp">
      <div class="mdl-card__title mdl-color--primary mdl-color-text--white">
        <h2 class="mdl-card__title-text">Welcome {{ user.username }}!</h2>
      </div>
      <div class="mdl-card__supporting-text">
        <div class="mdl-card__actions mdl-card--border">
          <a class="mdl-button" href='/profile'>GetProfile</a>
        </div>
      </div>
    </div>
  </main>
</div> 
```

#### 登录页面

在`views`文件夹中创建`login.hbs`，并添加以下代码。这是登录表格。

```
<!-- public/views/login.hbs -->
<div class="mdl-layout mdl-js-layout mdl-color--grey-100">
  <main class="mdl-layout__content">
    <div class="mdl-card mdl-shadow--6dp">
      <div class="mdl-card__title mdl-color--primary mdl-color-text--white">
        <h2 class="mdl-card__title-text">Nest Cats</h2>
      </div>
      <div class="mdl-card__supporting-text">
        <form action="/login" method="post">
          <div class="mdl-textfield mdl-js-textfield">
            <input class="mdl-textfield__input" type="text" name="username" id="username" />
            <label class="mdl-textfield__label" for="username">Username</label>
          </div>
          <div class="mdl-textfield mdl-js-textfield">
            <input class="mdl-textfield__input" type="password" name="password" id="password" />
            <label class="mdl-textfield__label" for="password">Password</label>
          </div>
          <div class="mdl-card__actions mdl-card--border">
            <button class="mdl-button mdl-button--colored mdl-js-button mdl-js-ripple-effect">Log In</button>
            <span class="mdl-textfield__error">{{ message }}</span>
          </div>
        </form>
      </div>
    </div>
  </main>
</div> 
```

#### 个人资料页面

在`views`文件夹中创建`profile.hbs`，并添加以下代码。此页面显示登录用户的详细信息。它呈现在我们受保护的路线上。

```
<!-- public/views/profile.hbs -->
<div class="mdl-layout mdl-js-layout mdl-color--grey-100">
  <main class="mdl-layout__content">
    <div class="mdl-card mdl-shadow--6dp">
      <div class="mdl-card__title mdl-color--primary mdl-color-text--white">
        <h2 class="mdl-card__title-text">About {{ user.username }}</h2>
      </div>
      <div>
        <figure><img src="http://lorempixel.com/400/200/cats/{{ user.pet.picId }}">
          <figcaption>{{ user.username }}'s friend {{ user.pet.name }}</figcaption>
        </figure>
        <div class="mdl-card__actions mdl-card--border">
          <a class="mdl-button" href='/logout'>Log Out</a>
        </div>
      </div>
    </div>
  </main>
</div> 
```

#### 设置视图引擎

现在我们告诉 Nest 使用 express-handlebar 作为视图引擎。我们在`main.ts`文件中完成这项工作，按照惯例，这是您的 Nest 应用程序启动的地方。虽然我们不会在这里详细讨论，但主要概念是:

1.  在`NestFactory.create()`方法调用中传递`<NestExpressApplication>`类型断言，以获得对本地 Express 方法的访问。
2.  完成这些之后，您现在可以简单地访问任何 Express 的本地`app`方法。在很大程度上，视图引擎和任何其他传统 Express 中间件的配置与下图所示的视图引擎设置一样正常。

修改`main.ts`文件，使其看起来像这样:

```
// src/main.ts
import { NestFactory } from '@nestjs/core';
import { NestExpressApplication } from '@nestjs/platform-express';
import { join } from 'path';
import { AppModule } from './app.module';
import * as exphbs from 'express-handlebars';

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  const viewsPath = join(__dirname, '../public/views');
  app.engine('.hbs', exphbs({ extname: '.hbs', defaultLayout: 'main' }));
  app.set('views', viewsPath);
  app.set('view engine', '.hbs');

  await app.listen(3000);
}
bootstrap(); 
```

### 认证路线

这一部分的最后一步是设置我们的路线。这是 MVC 架构开始大放异彩的一个领域。如果你习惯于裸露的高速公路，这可能会有点陌生，所以让我们涉水一点。请花几分钟时间阅读关于控制器的 [Nest 文档，了解更多关于 Nest 控制器如何工作的细节。](https://docs.nestjs.com/controllers)

我们将修改`app.controller.ts`，使它看起来像下面的大代码块。

在此之前，让我们花点时间看看下面大代码块中的`GET /`路由处理程序(以`@Get('/')`开始的行)。这项工作是由`@Render()`室内设计师完成的。因为我们在代码的第一次迭代中所做的只是呈现模板，所以代码很简单。`@Render()`采用单个参数，即要呈现的模板的名称。请将此视为类似于
的快速代码的嵌套等价物

```
app.get('/', function (req, res) {
    res.render('login');
}); 
```

用`@Render()`修饰的方法也可以返回一个提供模板变量的值。现在，我们只做一个空返回(隐式返回`undefined`)。稍后，我们将使用这个特性来传递模板变量。

继续用代码
更新`src/app.controller.ts`

```
// src/app.controller.ts
import { Controller, Get, Post, Res, Render } from '@nestjs/common';
import { Response } from 'express';

@Controller()
export class AppController {
  @Get('/')
  @Render('login')
  index() {
    return;
  }

  @Post('/login')
  login(@Res() res: Response): void {
    res.redirect('/home');
  }

  @Get('/home')
  @Render('home')
  getHome() {
    return;
  }

  @Get('/profile')
  @Render('profile')
  getProfile() {
    return;
  }

  @Get('/logout')
  logout(@Res() res: Response): void {
    res.redirect('/');
  }
} 
```

此时，您应该可以运行 app:

```
$ npm run start 
```

现在，浏览到 [http://localhost:3000](http://localhost:3000) ，点击基本 UI。当然，此时您可以在不登录的情况下点击页面。

### 实施护照策略

我们现在准备实现身份验证特性。很好地理解 Nest 如何与 Passport 集成是有帮助的。嵌套文档[在某种程度上覆盖了这一点](https://docs.nestjs.com/techniques/authentication#implementing-passport-strategies)。值得快速绕道去读那一节。

> 注意:我撰写了 NestJS 文档的这一部分，因此您将看到与下面的代码和文档有一些相似或重叠之处

关键要点是:

1.  Nest 提供了`@nestjs/passport`模块，*将*护照包装在一个 Nest 风格的包中，这使得将护照视为 [*提供商*](https://docs.nestjs.com/providers) 变得很容易。
2.  您通过扩展`PassportStrategy`类来实现 Passport *策略*，其中您实现了特定于策略的初始化和回调。

如前所述，我们将在这个用例中使用 passport-local 策略。我们一会儿将讨论这个实现。首先生成一个`AuthModule`，并在其中生成一个`AuthService` :

```
nest g module auth
nest g service auth 
```

我们还将实现一个`UsersService`来管理我们的*用户存储*，所以我们现在将生成那个模块和服务:

```
nest g module users nest g service users 
```

替换这些生成文件的默认内容，如下所示。对于我们的示例应用程序，`UsersService`只是维护一个硬编码的内存中用户列表，以及一个通过用户名检索用户的方法。在一个真实的应用程序中，这是你构建用户模型和持久层的地方，使用你选择的库(例如，TypeORM、Sequelize、Mongoose 等。).

```
// src/users/users.service.ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UsersService {
  private readonly users: any[];

  constructor() {
    this.users = [
      {
        userId: 1,
        username: 'john',
        password: 'changeme',
        pet: { name: 'alfred', picId: 1 },
      },
      {
        userId: 2,
        username: 'chris',
        password: 'secret',
        pet: { name: 'gopher', picId: 2 },
      },
      {
        userId: 3,
        username: 'maria',
        password: 'guess',
        pet: { name: 'jenny', picId: 3 },
      },
    ];
  }

  async findOne(username: string): Promise<any> {
    return this.users.find(user => user.username === username);
  }
} 
```

在`UsersModule`中，唯一的变化是将`UsersService`添加到`@Module`装饰器的导出数组中，这样它在这个模块之外是可见的(我们将很快在`AuthService`中使用它)。

你可以[在这里阅读更多关于 Nest 如何使用模块](https://docs.nestjs.com/modules)来组织代码，并了解更多关于`exports`数组和`@Module()`装饰器的其他参数。

确保`src/users/users.module.ts`看起来像这样:

```
// src/users/users.module.ts
import { Module } from '@nestjs/common';
import { UsersService } from './users.service';

@Module({
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {} 
```

我们的`AuthService`负责检索用户并验证密码。用下面的代码替换`src/auth/auth.service.ts`文件的默认内容:

```
// src/auth/auth.service.ts
import { Injectable } from '@nestjs/common';
import { UsersService } from '../users/users.service';

@Injectable()
export class AuthService {
  constructor(private readonly usersService: UsersService) {}

  async validateUser(username, pass): Promise<any> {
    const user = await this.usersService.findOne(username);
    if (user && user.password === pass) {
      const { password, ...result } = user;
      return result;
    }
    return null;
  }
} 
```

> **警告**当然，在真实的应用程序中，您不会以纯文本的形式存储密码。你可以使用像 [bcrypt](https://github.com/kelektiv/node.bcrypt.js#readme) 这样的库，使用加盐的单向散列算法。使用这种方法，您只需存储散列密码，然后将存储的密码与散列版本的**传入**密码进行比较，这样就不会以纯文本的形式存储或暴露用户密码。为了保持我们的示例应用程序简单，我们违反了绝对命令，使用纯文本。**不要在你的真实应用中这样做！**

我们稍后将从 passport-local strategy 子类中调用我们的`validateUser()`方法。Passport 库希望我们在验证成功时返回一个完整的用户，或者在验证失败时返回一个 null(失败的定义是找不到用户或者密码不匹配)。在我们的代码中，我们使用一个方便的 ES6 spread 操作符在返回用户对象之前从用户对象中剥离密码属性。验证成功后，Passport 会为我们处理一些细节，我们将在后面的 Sessions 部分中探讨这些细节。

最后，我们更新我们的`AuthModule`来导入`UsersModule`。

```
// src/auth/auth.module.ts
import { Module } from '@nestjs/common';
import { AuthService } from './auth.service';
import { UsersModule } from '../users/users.module';

@Module({
  imports: [UsersModule],
  providers: [AuthService],
})
export class AuthModule {} 
```

我们的应用程序现在可以运行了，但在我们完成更多步骤之前，它仍然不完整。你可以重启应用程序，导航到 [http://localhost:3000](http://localhost:3000) ，仍然可以在不登录的情况下四处移动(毕竟，我们还没有实现我们的 passport-local 策略。我们马上就到)。

#### 实施护照本地化

现在我们可以实施我们的护照-本地策略了。在`auth`文件夹中创建一个名为`local.strategy.ts`的文件，并添加以下代码:

```
// src/auth/local.strategy.ts
import { Strategy } from 'passport-local';
import { PassportStrategy } from '@nestjs/passport';
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { AuthService } from './auth.service';

@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private readonly authService: AuthService) {
    super();
  }

  async validate(username: string, password: string) {
    const user = await this.authService.validateUser(username, password);
    if (!user) {
      throw new UnauthorizedException();
    }
    return user;
  }
} 
```

我们遵循了 [NestJS 认证章节](https://docs.nestjs.com/techniques/authentication#implementing-passport-strategies)中描述的方法。在这个使用 passport-local 的用例中，没有配置选项，所以我们的构造函数简单地调用了`super()`，没有使用 options 对象。

我们还实现了`validate()`方法。对于本地策略，Passport 期望一个带有类似
签名的`validate()`方法

```
validate(username: string, password:string): any 
```

大部分工作是在我们的`AuthService`(反过来，在我们的`UserService`)中完成的，所以这个方法非常简单。任何 T4 护照策略的 T2 方法都将遵循类似的模式。如果找到了一个有效的用户，它将被返回，这样请求处理就可以继续，Passport 可以做一些进一步的管理工作。如果没有找到，我们抛出一个异常，让 [Nest 的异常层](https://docs.nestjs.com/exceptions)处理它。

制定好战略后，我们还有几项任务要完成:

1.  创建用于装饰路线的防护，以便调用配置的 Passport 策略
2.  根据需要添加装饰者
3.  实现会话，以便用户跨请求保持登录状态
4.  配置 Nest 以使用 Passport 和与会话相关的功能
5.  给用户体验添加一点润色

让我们开始吧。对于接下来的部分，我们将希望遵循最佳实践项目结构，所以从创建几个文件夹开始。在`src`下，创建一个`common`文件夹。在`common`里面，创建`filters`和`guards`文件夹。我们的项目结构现在看起来是这样的:

```
mvc-sessions
└───src
│   └───auth
│   └───common
│       └───filters
│       └───guards
│   └───users
└───public 
```

### 弈卫

NestJS [Guards 章节](https://docs.nestjs.com/guards)描述了 Guards 的主要功能:决定一个请求是否将被路由处理器处理。这仍然是事实，我们将很快使用该功能。然而，在使用`@nestjs/passport`模块的上下文中，我们还将引入一个新的小问题，这个问题一开始可能会令人困惑，所以现在让我们来讨论一下。同样，NestJS 认证一章有一个[部分描述了这个场景](https://docs.nestjs.com/techniques/authentication#built-in-passport-guards)，现在读起来会很不错。关键要点是:

1.  Passport 支持两种*模式*的认证。首先，您需要执行认证步骤(即*登录*
2.  随后，您需要*验证用户的凭证*。对于 passport-local，这意味着确保用户的会话是有效的。

**这两个**步骤都是通过嵌套**守卫**实现的。

#### 初始认证

查看我们的 UI，很容易看到我们将通过在我们的`/login`路线上的`POST`请求来处理这个初始认证步骤。那么，我们如何在该路线中调用 passport-local 策略的“登录阶段”呢？正如所建议的，答案是使用一个警卫。类似于我们在上一节中扩展`PassportStrategy`类的方式，我们将从`@nestjs/passport`包中提供的默认`AuthGuard`开始，并根据需要扩展它。我们将命名我们的新守卫`LoginGuard`。然后，我们将用这个`LoginGuard`来装饰我们的`POST /login`路由，以调用 passport-local 策略的登录阶段。

在`guards`文件夹中创建一个名为`login.guard.ts`的文件，并替换其默认内容如下:

```
// src/common/guards/login.guard.ts
import { ExecutionContext, Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class LoginGuard extends AuthGuard('local') {
  async canActivate(context: ExecutionContext) {
    const result = (await super.canActivate(context)) as boolean;
    const request = context.switchToHttp().getRequest();
    await super.logIn(request);
    return result;
  }
} 
```

这几行代码中有很多内容，所以让我们从头到尾看一遍。

*   我们的 passport-local 策略有一个默认名称“local”。我们在正在定义的`LoginGuard`的`extends`子句中引用该名称，以便将我们的自定义防护绑定到由`passport-local`包提供的代码。如果我们最终在应用程序中使用多个 Passport 策略(每个策略都可能贡献一个特定的策略`AuthGuard`)，这需要区分我们扩展的是哪个类。
*   和所有的守卫一样，我们定义/覆盖的主要方法是`canActivate()`，这就是我们在这里所做的。你可以在这里阅读更多关于[守卫和定制`canActivate()`方法](https://docs.nestjs.com/guards)。
*   关键部分发生在`canActivate()`的主体中，在这里我们建立了一个快速会话。事情是这样的:
    *   我们在超类上调用`canActivate()`，就像我们通常在扩展类方法时一样。我们的超类提供了调用 passport-local 策略的框架。回想一下[守卫](https://docs.nestjs.com/guards)一章，其中`canActivate()`返回一个布尔值，指示是否调用目标路线。当我们到达这里时，Passport 将已经运行了之前配置的策略(来自超类),并将返回一个布尔值来指示用户是否已经成功地通过了身份验证。在这里，我们将结果存储起来，以便在最终从我们的方法返回之前可以做更多的处理。
    *   启动会话的关键步骤是现在调用我们的超类上的`logIn()`方法，传入当前请求。这实际上调用了 Passport 在前一步中自动添加到我们的 Express `Request`对象中的一个特殊方法。查看[此处](http://www.passportjs.org/docs/configure/)和[此处](http://www.passportjs.org/docs/login/)了解更多关于护照课程和这些特殊方法的信息。
    *   现在已经建立了 Express 会话，我们可以返回我们的`canActivate()`结果，只允许经过身份验证的用户继续。

### 会话

现在我们已经介绍了会话，还有一个额外的细节需要注意。会话是一种将唯一用户与该用户的一些服务器端状态信息相关联的方式。让我们简单地研究一下 Passport 是如何运行在 Express sessions 之上的，以提供一些背景信息。

Passport 向`session`对象添加属性，以跟踪关于用户及其身份验证状态的信息。用户详细信息通过对 Passport 库的`serializeUser()`调用来填充。这个函数由 Nest 用在`validate()`方法中创建的`user`对象自动调用(几分钟前我们在`src/auth/local.strategy.ts`中实现了这个方法)。这种方法起初可能感觉有点复杂，但是它支持一种完全灵活的模型，让您可以管理与您的*用户存储*的交互。在我们的例子中，我们只是原封不动地传递了`user`对象。在高级场景中，您可能会发现自己正在调用数据库或缓存层来为用户对象增加更多信息(例如，角色/权限)。除非您正在做类似的高级工作，否则通常可以使用下面的序列化过程样板文件。

在`auth`文件夹中创建`session.serializer.ts`文件，并添加以下代码:

```
// src/auth/session.serializer.ts
import { PassportSerializer } from '@nestjs/passport';
import { Injectable } from '@nestjs/common';
@Injectable()
export class SessionSerializer extends PassportSerializer {
  serializeUser(user: any, done: (err: Error, user: any) => void): any {
    done(null, user);
  }
  deserializeUser(payload: any, done: (err: Error, payload: string) => void): any {
    done(null, payload);
  }
} 
```

我们需要配置我们的`AuthModule`来使用我们刚刚定义的 Passport 特性。当然`AuthService`和`LocalStrategy`作为*提供者*是有意义的(如果你需要，在这里阅读[更多关于提供者的信息)。注意，我们刚刚创建的`SessionSerializer`也是一个可插拔的提供者，需要包含在`providers`数组中。如果现在还不是 100%清楚，也不要担心。只需将*提供者*视为将可定制服务注入您的应用程序结构(包括您已经包含的第三方模块)的通用方式。](https://docs.nestjs.com/providers)

将`auth.module.ts`更新成这样:

```
// src/auth/auth.module.ts
import { Module } from '@nestjs/common';
import { AuthService } from './auth.service';
import { UsersModule } from '../users/users.module';
import { PassportModule } from '@nestjs/passport';
import { LocalStrategy } from './local.strategy';
import { SessionSerializer } from './session.serializer';

@Module({
  imports: [UsersModule, PassportModule],
  providers: [AuthService, LocalStrategy, SessionSerializer],
})
export class AuthModule {} 
```

现在让我们创建我们的`AuthenticatedGuard`。这是一个传统的守卫，在 NestJS [守卫章节](https://docs.nestjs.com/guards)中有介绍。它的作用只是保护某些路线。在`guards`文件夹中创建文件`authenticated.guard.ts`，并添加以下代码:

```
// src/common/guards/authenticated.guard.ts
import { ExecutionContext, Injectable, CanActivate } from '@nestjs/common';

@Injectable()
export class AuthenticatedGuard implements CanActivate {
  async canActivate(context: ExecutionContext) {
    const request = context.switchToHttp().getRequest();
    return request.isAuthenticated();
  }
} 
```

这里唯一需要指出的是，为了确定一个用户是否通过了身份验证，我们使用了 Passport 为我们附加到`request`对象上的便捷的`isAuthenticated()`方法。只有当用户通过验证(即具有有效会话)时，Passport 才会返回`true`。

### 配置嵌套引导功能

我们现在可以告诉 Nest 使用我们已经配置好的 Passport 特性。将`main.ts`更新成这样:

```
// src/main.ts
import { NestFactory } from '@nestjs/core';
import { NestExpressApplication } from '@nestjs/platform-express';
import { join } from 'path';
import { AppModule } from './app.module';

import * as session from 'express-session';
import flash = require('connect-flash');
import * as exphbs from 'express-handlebars';
import * as passport from 'passport';

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);

  const viewsPath = join(__dirname, '../public/views');
  app.engine('.hbs', exphbs({ extname: '.hbs', defaultLayout: 'main' }));
  app.set('views', viewsPath);
  app.set('view engine', '.hbs');

  app.use(
    session({
      secret: 'nest cats',
      resave: false,
      saveUninitialized: false,
    }),
  );

  app.use(passport.initialize());
  app.use(passport.session());
  app.use(flash());

  await app.listen(3000);
}
bootstrap(); 
```

这里，我们已经将会话和 Passport 支持添加到我们的 Nest 应用程序中。

> **警告**和往常一样，一定要在你的源代码中隐藏秘密(**不要把你的会话秘密放在代码中，就像我们在这里做的那样；使用环境变量或配置模块(如 [NestJS 配置管理器](https://github.com/nestjsplus/config)代替**)。

请注意，顺序很重要(首先注册会话中间件，然后初始化 Passport，然后配置 Passport 以使用会话)。几分钟后我们将看到`flash`功能的使用。

#### 添加路线守卫

现在我们准备开始在路线上使用这些防护。将`app.controller.ts`更新成这样:

```
// src/app.controller.ts
import { Controller, Get, Post, Request, Res, Render, UseGuards } from '@nestjs/common';
import { Response } from 'express';

import { LoginGuard } from './common/guards/login.guard';
import { AuthenticatedGuard } from './common/guards/authenticated.guard';

@Controller()
export class AppController {
  @Get('/')
  @Render('login')
  index() {
    return;
  }

  @UseGuards(LoginGuard)
  @Post('/login')
  login(@Res() res: Response) {
    res.redirect('/home');
  }

  @UseGuards(AuthenticatedGuard)
  @Get('/home')
  @Render('home')
  getHome(@Request() req) {
    return { user: req.user };
  }

  @UseGuards(AuthenticatedGuard)
  @Get('/profile')
  @Render('profile')
  getProfile(@Request() req) {
    return { user: req.user };
  }

  @Get('/logout')
  logout(@Request() req, @Res() res: Response) {
    req.logout();
    res.redirect('/');
  }
} 
```

上面，我们已经导入了两个新的守卫，并恰当地应用了它们。我们使用我们的`POST /login`路由上的`LoginGuard`来启动 passport-local 策略中的认证序列。我们在受保护的路由上使用`AuthenticatedGuard`,以确保未经授权的用户无法访问它们。

我们还利用了 Passport 特性，该特性自动将我们的`User`对象作为`req.user`存储在`Request`对象上。有了这个便利的特性，我们现在可以在用`@Render()`修饰的路线上返回值，以将一个变量传递到我们的车把模板中来定制它们的内容。
例如`return { user: req.user }`在我们的`home`模板中显示`User`对象的信息。

最后，我们已经在我们的`logout`路由中添加了对`req.logout()`的呼叫。这依赖于 Passport `logout()`函数，该函数与我们之前在 Sessions 部分讨论的`logIn()`方法一样，在成功认证后，已经由 Passport 自动添加到 Express `Request`对象中。当我们调用`logout()`时，Passport 会为我们拆除会话。

现在，您应该能够通过尝试导航到受保护的路由来测试身份验证逻辑。重启应用，将浏览器指向[http://localhost:3000/profile](http://localhost:3000/profile)。您应该会得到一个`403 Forbidden`错误。返回到 [http://localhost:3000](http://localhost:3000) 的根页面，登录，你应该可以浏览(尽管这个应用仍然缺少一些功能)。参考`src/users/users.service.ts`了解接受的硬编码用户名和密码。

### 添加上光剂

让我们解决那个丑陋的 403 禁止错误页面。如果你浏览这个应用，尝试提交一个空的登录请求，一个错误的密码，然后注销，你会发现这不是一个很好的 UX。让我们注意一些事情:

1.  每当用户身份验证失败，或者当他们退出应用程序时，让我们将用户返回到登录页面
2.  让我们在用户输入不正确的密码时提供一点反馈

处理第一个需求的最好方法是实现一个[过滤器](https://docs.nestjs.com/exception-filters)。在`filters`文件夹中创建文件`auth-exceptions.filter.ts`，并添加以下代码:

```
// src/common/filters/auth-exceptions.filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  UnauthorizedException,
  ForbiddenException,
} from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class AuthExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    if (
      exception instanceof UnauthorizedException ||
      exception instanceof ForbiddenException
    ) {
      request.flash('loginError', 'Please try again!');
      response.redirect('/');
    } else {
      response.redirect('/error');
    }
  }
} 
```

nestj[过滤器章节](https://docs.nestjs.com/exception-filters)中唯一的新元素是`connect-flash`的使用。如果一个路由返回一个`UnauthorizedException`或者一个`ForbiddenException`，我们用`response.redirect('/')`重定向到根路由。我们还使用`connect-flash`在 Passport 的会话中存储消息。这种机制允许我们在重定向时临时保存消息。Passport 和`connect-flash`会自动处理存储、检索和清理这些信息的细节。

最后一步是在我们的把手模板中显示 flash 消息。更新`app.controller.ts`成这样。在这次更新中，我们将添加`AuthExceptionFilter`并将闪光参数添加到我们的索引(`/`)路线中。

```
// src/app.controller.ts
import { Controller, Get, Post, Request, Res, Render, UseGuards, UseFilters } from '@nestjs/common';
import { Response } from 'express';

import { LoginGuard } from './common/guards/login.guard';
import { AuthenticatedGuard } from './common/guards/authenticated.guard';
import { AuthExceptionFilter } from './common/filters/auth-exceptions.filter';

@Controller()
@UseFilters(AuthExceptionFilter)

export class AppController {
  @Get('/')
  @Render('login')
  index(@Request() req): { message: string } {
    return { message: req.flash('loginError') };
  }

  @UseGuards(LoginGuard)
  @Post('/login')
  login(@Res() res: Response) {
    res.redirect('/home');
  }

  @UseGuards(AuthenticatedGuard)
  @Get('/home')
  @Render('home')
  getHome(@Request() req) {
    return { user: req.user };
  }

  @UseGuards(AuthenticatedGuard)
  @Get('/profile')
  @Render('profile')
  getProfile(@Request() req) {
    return { user: req.user };
  }

  @Get('/logout')
  logout(@Request() req, @Res() res: Response) {
    req.logout();
    res.redirect('/');
  }
} 
```

对于我们的服务器端 Web 应用程序，我们现在有了一个全功能的身份验证系统。继续启动它，[尝试登录和退出](http://localhost:3000)，在退出时访问受保护的路由，并键入错误/丢失的用户名/密码字段，并注意更友好的错误处理。

我们完了！工藤是为了坚持完成一个**长**的教程。我希望这有助于您理解使用 NestJS 实现会话和认证的一些技术。

### 资源

你可以在 github 上找到这篇文章[的所有源代码。](https://github.com/johnbiundo/mvc-sessions)

请随意提问，发表意见或建议，或者在下面的评论中说声你好。请参加我们的 [Discord](https://discord.gg/G7Qnnhy) 节目，享受更多关于 NestJS 的快乐讨论。我在那里发布的名字是 *Y 前景*。

### 鸣谢

感谢[杰伊·麦克唐纳](https://github.com/jmcdo29)、[里维奥·布鲁纳](https://github.com/BrunnerLivio)和[卡米尔·米利维克](https://github.com/kamilmysliwiec)帮助审阅本文。