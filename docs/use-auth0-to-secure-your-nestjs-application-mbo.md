# 使用 Auth0 保护您的 NestJS 应用程序

> 原文：<https://dev.to/matthias/use-auth0-to-secure-your-nestjs-application-mbo>

**TL；DR:** 在这篇文章中，我将解释如何用 [Auth0](https://auth0.com) 保护你的基于 [NestJS](https://nestjs.com) 的 API。除此之外，还包括 Auth0 管理 API 来接收当前登录用户的配置文件。
如果你不想读这篇文章，你可以直接前往 [GitHub 库](https://github.com/fullstack-to/nestjs-auth0-jwt)。

* * *

上周，我努力将 Auth0 连接到我的 NestJS 项目。我想限制我的 API 的某些端点对公众开放。
由于不想在自己的服务器上存储用户数据和密码，所以决定使用 Auth0。

我使用 NestJS 作为后端应用程序的框架已经有一段时间了。NestJS 让新用户非常容易上手。它提供了一个成熟的 CLI，许多有用的功能已经可用，可以根据需要集成到项目中(例如，数据库连接、验证、日志或 http 请求)。

我绝望了，找不到可行的解决办法。我在 Twitter 和 dev.to 上寻求帮助:

## 文章不再可用

液体错误:内部

我收到了一些直接的信息，在那里我得到了非常有用的建议。不幸的是，我无法做到，所以我决定休息几天，不再去想它。
这被证明是一个非常好的主意，最终我解决了🥳.的问题

## 配置 Auth0

如果你还没有一个 Auth0 帐户，你应该去他们的[注册](https://auth0.com/signup)页面创建一个。
Auth0 提供了一个慷慨的免费层，提供无密码用户账户、两个社交身份提供商(如谷歌、脸书、Twitter 或 GitHub)以及他们的[社区](https://community.auth0.com/)的支持。对于本教程和较小的辅助项目，自由计划应该足够了。

### 创建 API

当您登录到您的仪表板时，导航到*API*部分，点击*创建 API* 按钮创建一个新的 API。

[![Create API](img/5425d19d491d474cb5754af471dbbb66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_jtreUK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j0ub4dnfcnyi7nhy3apk.png)

然后提示您输入 API 名称和标识符。Auth0 建议使用 url 作为 API 的标识符和友好名称。
签名算法应保留默认选项(RS256)。

[![Create API](img/b495db2cbda94219f65e3454750ee9b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HKKlEVl3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ny6w1m005plfhzspvbg.png)

### 创建应用程序

创建新应用程序时，Auth0 会询问类型。对于这个例子，类型并不重要。对于一个公开可用的应用程序，您可能想要阅读更多关于[应用程序类型](https://auth0.com/docs/applications/concepts/app-types-auth0)的信息，以选择一个适合您的目的的类型。

[![Create Application](img/eacd209d17b707226eac0d369286a502.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qt5fx-Of--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fe542x2850n2v0hfw4rh.png)
[![Create Application](img/2e6acc726810e361177173668097f9ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eCscMcki--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/shfxkru40u5nlvx20dty.png)
[![Create Application](img/84321437df455c591d481af53e3938da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C8o2TBx2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j5ec6rc0024pwodtdghe.png)

创建应用程序后，您需要配置一个允许的回调 URL。回调 URL 是用户在成功通过身份验证后可以被重定向到的位置。

[![Add Callback URL](img/8c327480729e3bc2cf07d39d3e476a04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2imRHbPS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u25uf4bjvv7sdjvgg8ck.png)

### 启用授权 0 管理 API

因为我们稍后还想读取经过身份验证的用户的配置文件，所以我们必须为刚刚创建的应用程序激活 Auth0 管理 API。
激活 Auth0 管理 API 的配置可以在*API*选项卡中找到。
此外，*显示:必须选择用户*范围。

[![Enable Auth0 Management API ](img/56405d5bc76761fde23bee1400f530aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q1s_Wwgz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/85m0yysugi3k8eghna8f.png)

### 登录

我们将需要生成一个访问令牌，以便稍后使用该 API。令牌是通过 Auth0 登录表单生成的。
登录表单由 Auth0 提供，可在您的 Auth0 租户域上访问。
您还需要您的 Auth0 客户端 id。这两个值都可以从应用程序配置页面中复制。

[![Get Variables](img/e2b05148085ac8dff3a9f18b02ab6252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DSqYDMh5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dml6voyppxw5by3y5b3x.png)

替换 URL 中的值，并在浏览器中打开它。

`https://$AUTH0_DOMAIN/authorize?audience=http://localhost:3000&scope=SCOPE&response_type=code&client_id=$AUTH0_CLIENT_ID&redirect_uri=http://localhost:4200/login&state=STATE?prompt=none`

[![Login](img/9bde591b454872cda89e216d5036f700.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AIw5r7Oq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n9u595lkwfcfyrsk9oa0.png)

在您登录 SSO 提供商(默认配置 Google)或使用电子邮件和密码后。
登录表单会将你重定向到[http://localhost:4200/log in](http://localhost:4200/login)，这是我们之前配置的。
我们不关心是否有 web 应用程序正在运行，但是您需要复制`code`查询参数的值并保存它以备后用。
需要`code`来生成不记名令牌。

[![Login](img/fc67d1da07ffff017075a3682c19263c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sgSe1k7x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/72t8wtk1f9ghtw6di5oa.png)

### 更新用户元数据

Auth0 的一个优点是，您可以将元数据附加到用户配置文件中。你可以使用用户元数据将用户档案链接到其他服务(例如 GitHub 或 dev . to profile)——我相信你会找到许多其他的用例。

[![User](img/083456bd74fc23b470177f7e247fe2c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZnVTTq7---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l3dphqlgqgex9rp83mpj.png)
[![User](img/12b51e13c7a50698de8515145ccb9c8e.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZiTWotr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r80kcbob70v8c3lr58sn.png)

* * *

## 创建新的 NestJS 应用

```
$ npm i -g @nestjs/cli
$ nest new nestjs-auth0-jwt 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利的话，一个新的 NestJS 应用程序就创建好了，其目录结构如下:

```
nestjs-auth0-jwt
├── src
│   ├── app.controller.spec.ts
│   ├── app.controller.ts
│   ├── app.module.ts
│   ├── app.service.ts
│   └── main.ts
├── test
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── README.md
├── nest-cli.json
├── package-lock.json
├── package.json
├── tsconfig.build.json
├── tsconfig.json
└── tslint.json 
```

Enter fullscreen mode Exit fullscreen mode

### 运行应用

导航到新目录并启动 API:

```
$ cd nestjs-auth0-jwt
$ npm run start:dev 
```

Enter fullscreen mode Exit fullscreen mode

一旦应用程序开始运行，您就可以打开浏览器，进入 [http://localhost:3000](http://localhost:3000) 。

[![Run Application](img/4e39064442f31b0905d245f0a5a792c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--axb2xOol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p0c9xtcpjbve39suhhmk.png)

### 添加依赖关系

```
$ npm install --save @nestjs/passport passport passport-jwt jwks-rsa auth0
$ npm install --save-dev @types/passport-jwt @types/auth0 
```

Enter fullscreen mode Exit fullscreen mode

### 创建授权模块

```
$ nest generate module auth 
```

Enter fullscreen mode Exit fullscreen mode

### 打造 JWT 战略

```
$ touch auth/jwt.strategy.ts 
```

Enter fullscreen mode Exit fullscreen mode

```
import { passportJwtSecret } from 'jwks-rsa';
import { ExtractJwt, Strategy, VerifiedCallback } from 'passport-jwt';

import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      secretOrKeyProvider: passportJwtSecret({
        cache: true,
        rateLimit: true,
        jwksRequestsPerMinute: 5,
        jwksUri: `https://${process.env.AUTH0_DOMAIN}/.well-known/jwks.json`
      }),

      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(), // 1
      audience: 'http://localhost:3000',
      issuer: `https://${process.env.AUTH0_DOMAIN}/`
    });
  }

  validate(payload: any, done: VerifiedCallback) {
    if (!payload) {
      done(new UnauthorizedException(), false); // 2
    }

    return done(null, payload);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

1: `ExtractJwt.fromAuthHeaderAsBearerToken()`:创建一个新的提取器，该提取器使用“bearer”方案在授权头中查找 JSON Web 令牌。

2:如果没有接收到有效载荷，则认证失败。

### 更新授权模块

```
import { Module } from '@nestjs/common';
import { PassportModule } from '@nestjs/passport';

import { JwtStrategy } from './jwt.strategy';

@Module({
  imports: [PassportModule],
  providers: [JwtStrategy],
  exports: [JwtStrategy]
})
export class AuthModule {} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 API 端点

```
import { ManagementClient, User } from 'auth0';
import * as express from 'express';

import { Controller, Get, Request, UseGuards } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    return this.appService.getHello(); // 1
  }

  @Get('secret')
  @UseGuards(AuthGuard('jwt')) // 2
  secretEndpoint(@Request() req: express.Request): string {
    return 'this endpoint should be protected';
  }

  @Get('profile')
  @UseGuards(AuthGuard('jwt')) // 2
  async profile(@Request() req: express.Request): Promise<any> {
    const authZero = new ManagementClient({
      // 3
      domain: process.env.AUTH0_DOMAIN,
      clientId: process.env.AUTH0_CLIENT_ID,
      clientSecret: process.env.AUTH0_CLIENT_SECRET,
      scope: 'read:users update:users'
    });

    const response = await authZero
      .getUser({ id: req.user.sub }) // 4
      .then((user: User) => {
        return user;
      })
      .catch(err => {
        return err;
      });

    return response;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

1:公共可用 API 端点。

2: `@UseGuards(AuthGuard('jwt'))`

3:创建 Auth0 管理 API 客户端。

4:JWT 响应中的主题与 Auth0 用户 ID 相同。因此，`sub`属性被用作`getUser`调用的`id`参数，该调用返回整个用户配置文件。

### 使用登录码检索不记名令牌

```
$ export AUTH0_DOMAIN=${AUTH0_DOMAIN}
$ export AUTH0_CLIENT_ID=${AUTH0_CLIENT_ID}
$ export AUTH0_CLIENT_SECRET=${AUTH0_CLIENT_SECRET}
$ export CODE=${CODE}

$ curl -X POST -H 'content-type: application/json' -d '{
  "grant_type": "authorization_code",
  "client_id": "'$AUTH0_CLIENT_ID'",
  "client_secret": "'$AUTH0_CLIENT_SECRET'",
  "code": "'$CODE'",
  "redirect_uri": "http://localhost:4200"
}' https://$AUTH0_DOMAIN/oauth/token 
```

Enter fullscreen mode Exit fullscreen mode

```
{  "access_token":  "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9EQXpOMEZHTWpBd1FVUTJORFpETXpBMVJETXdSRUUyTjBSRE5qRXpNemRFUWtNMk5URTFNUSJ9.eyJpc3MiOiJodHRwczovL25lc3Rqcy1hdXRoMC1qd3QuYXUuYXV0aDAuY29tLyIsInN1YiI6Imdvb2dsZS1vYXV0aDJ8MTEzMDc2NzYwNTUyNTQ5MTY1ODQ5IiwiYXVkIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwIiwiaWF0IjoxNTY1OTg1MzU3LCJleHAiOjE1NjYwNzE3NTcsImF6cCI6IjR0eFZVSm5ZVlRhbzRsczA2dFozRGNCdm5JVk9uY01RIn0.U3dVcaFPY5ZHlz9sntx3O2Svz_HfapBpryrUB9ipIdoIHgu_Skp40FGaLu0Fx-_Uo2GE4pfvM-XR1bQgQGyVbMhALpe2CZrKKCe9k1v4VZ_zxwhYDdV8WNr99jmbMtnm_I9rZIz3YU9dyjWlV_ktHV0bPHj1wIjBUrUc9P_EF5Vw3CeNMxlFXZ2xYldT9XdYUotJHIoJ-e_KWo0hMn_qF5xvWxD-RJIQL7G2ZxEcsmMe9JovwZoAnoqaIyutMP8g7X1UfoTGs-Fa6B1xXhtrYBms--sm_FrM5w0rjIyOuyujulPTeXO8_CbuL1Yz5kCcBsuJdXTiyTcTV9R8W0f4Rg",  "expires_in":  86400,  "token_type":  "Bearer"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 运行应用

用之前保存的值替换变量`${AUTH0_DOMAIN}`、`${AUTH0_CLIENT_ID}`和`${AUTH0_CLIENT_SECRET}`，并启动 NestJS 应用程序。

```
$ export AUTH0_DOMAIN=${AUTH0_DOMAIN}
$ export AUTH0_CLIENT_ID=${AUTH0_CLIENT_ID}
$ export AUTH0_CLIENT_SECRET=${AUTH0_CLIENT_SECRET}
$ npm run start:dev 
```

Enter fullscreen mode Exit fullscreen mode

### 发出请求而不进行认证

应用程序运行后，使用 cURL 访问 API 端点`/secret`和`/profile`。
您将得到一个 401(未授权)错误，因为两个端点都被标上了`AuthGuard`注释，因此不能公开使用。

```
$ curl http://localhost:3000/secret 
```

Enter fullscreen mode Exit fullscreen mode

```
$ curl http://localhost:3000/profile 
```

Enter fullscreen mode Exit fullscreen mode

### 提出认证请求

现在您可以运行相同的请求，但是通过`Authorization`头提供访问令牌。

因为您之前授权了自己，所以现在可以寻址两个私有端点`/secret`和`/profile`。

`/profile`端点还从 Auth0 返回您的配置文件信息，这是我们从 Auth0 管理 API 接收的。

Auth0 管理 API 使用 JWT 响应中的`sub`(主题)字段来检索您的用户配置文件。
由于文档的原因，示例应用程序中的 JWT 响应被记录到控制台。

```
$ curl http://localhost:3000/secret \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9EQXpOMEZHTWpBd1FVUTJORFpETXpBMVJETXdSRUUyTjBSRE5qRXpNemRFUWtNMk5URTFNUSJ9.eyJpc3MiOiJodHRwczovL25lc3Rqcy1hdXRoMC1qd3QuYXUuYXV0aDAuY29tLyIsInN1YiI6Imdvb2dsZS1vYXV0aDJ8MTEzMDc2NzYwNTUyNTQ5MTY1ODQ5IiwiYXVkIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwIiwiaWF0IjoxNTY1OTg1MzU3LCJleHAiOjE1NjYwNzE3NTcsImF6cCI6IjR0eFZVSm5ZVlRhbzRsczA2dFozRGNCdm5JVk9uY01RIn0.U3dVcaFPY5ZHlz9sntx3O2Svz_HfapBpryrUB9ipIdoIHgu_Skp40FGaLu0Fx-_Uo2GE4pfvM-XR1bQgQGyVbMhALpe2CZrKKCe9k1v4VZ_zxwhYDdV8WNr99jmbMtnm_I9rZIz3YU9dyjWlV_ktHV0bPHj1wIjBUrUc9P_EF5Vw3CeNMxlFXZ2xYldT9XdYUotJHIoJ-e_KWo0hMn_qF5xvWxD-RJIQL7G2ZxEcsmMe9JovwZoAnoqaIyutMP8g7X1UfoTGs-Fa6B1xXhtrYBms--sm_FrM5w0rjIyOuyujulPTeXO8_CbuL1Yz5kCcBsuJdXTiyTcTV9R8W0f4Rg' 
```

Enter fullscreen mode Exit fullscreen mode

```
$ curl http://localhost:3000/profile \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9EQXpOMEZHTWpBd1FVUTJORFpETXpBMVJETXdSRUUyTjBSRE5qRXpNemRFUWtNMk5URTFNUSJ9.eyJpc3MiOiJodHRwczovL25lc3Rqcy1hdXRoMC1qd3QuYXUuYXV0aDAuY29tLyIsInN1YiI6Imdvb2dsZS1vYXV0aDJ8MTEzMDc2NzYwNTUyNTQ5MTY1ODQ5IiwiYXVkIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwIiwiaWF0IjoxNTY1OTg1MzU3LCJleHAiOjE1NjYwNzE3NTcsImF6cCI6IjR0eFZVSm5ZVlRhbzRsczA2dFozRGNCdm5JVk9uY01RIn0.U3dVcaFPY5ZHlz9sntx3O2Svz_HfapBpryrUB9ipIdoIHgu_Skp40FGaLu0Fx-_Uo2GE4pfvM-XR1bQgQGyVbMhALpe2CZrKKCe9k1v4VZ_zxwhYDdV8WNr99jmbMtnm_I9rZIz3YU9dyjWlV_ktHV0bPHj1wIjBUrUc9P_EF5Vw3CeNMxlFXZ2xYldT9XdYUotJHIoJ-e_KWo0hMn_qF5xvWxD-RJIQL7G2ZxEcsmMe9JovwZoAnoqaIyutMP8g7X1UfoTGs-Fa6B1xXhtrYBms--sm_FrM5w0rjIyOuyujulPTeXO8_CbuL1Yz5kCcBsuJdXTiyTcTV9R8W0f4Rg' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我希望本文能让您了解如何在您的 NestJS 应用程序中实现像 Auth0 这样的身份验证提供者。

对我来说，在我的项目中集成 Auth0 和 authentication 非常麻烦，但最终我让它工作了，这感觉真的很棒。

> ![Matthias 🧔 profile image](img/b1ec38a41528706423a4675eddade26c.png)马提亚斯·🧔@ full stack _ to![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ nest framework](https://twitter.com/nestframework)[@ auth 0](https://twitter.com/auth0)[@ kammysliwiec](https://twitter.com/kammysliwiec)🥳我终于搞定了！
> 
> 现在我可以用 Auth0 保护一个 API 端点，并且我有了另一个端点，其中 JWT 令牌所有者的配置文件是从 Auth0 获取的👏
> 
> 我将在 [dev.to](https://t.co/5lD2d1RTb8) 上写几行我是如何完成的！📝2019 年 8 月 15 日下午 18:25[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1162067806109732864)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1162067806109732864)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1162067806109732864)

> ![Matthias 🧔 profile image](img/b1ec38a41528706423a4675eddade26c.png)马提亚斯·🧔@ full stack _ to![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ nest framework](https://twitter.com/nestframework)[@ auth 0](https://twitter.com/auth0)[@ kammysliwiec](https://twitter.com/kammysliwiec)这些时刻提醒我为什么我热爱编码❤️
> 而且，有时停止某个特定问题一段时间的工作以获得新的视角是有意义的。2019 年 8 月 15 日下午 18:27[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1162068298395258880)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1162068298395258880)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1162068298395258880)

[![matthias profile image](img/c388b8fe3100715731da3d929f42f97f.png) ](/matthias) [ Matthias 🤖 ](/matthias) • [<time datetime="2019-08-17T07:45:24Z"> Aug 17 '19 </time>](https://dev.to/matthias/comment/e8an) 

我无法在我的 NestJS 项目中获得 Auth0 认证。我也在这里寻求帮助:

[![fullstack_to image](img/1be7c3a03ee5af90f231eb5d35ad56d2.png)](/fullstack_to) [## 对 NestJS 使用 JWT 身份验证(Auth0)🔐

### 马提亚🤖8 月 14 日 1 分钟读取

#help #webdev #security](/fullstack_to/using-jwt-authentication-auth0-with-nestjs-54oo)

我在这个问题上挣扎了几天，然后休息了一段时间，一天左右没有碰我的代码。我带着干净的头脑重新开始，并能在一小时内解决问题🥳.

这就是为什么我喜欢编程，当你有所成就的时候，你总会有这种伟大的时刻💪
和*通常*每隔几个小时或几天(取决于你的项目规模)你就会有这个小小的突破。对我来说，这是我工作中最有动力的事情！

顺便说一句:我正在写一篇关于我如何解决它的文章😉。

我还认为关于认证的 NestJS 文档可以改进，使人们更容易正确地实现它，避免安全漏洞。然而，我不抱怨，NestJS 是开源的，每个人都可以[改进文档。](https://github.com/nestjs/docs.nestjs.com/edit/master/content/techniques/authentication.md)

如果你对 NestJS、Auth0 或其他话题有任何疑问，请不要犹豫，在 [Twitter](https://twitter.com/fullstack_to) 、 [dev.to](https://dev.to/connect/@fullstack_to) 上给我写 DM，或者给我写邮件: [yo@fullstack.to](//mailto:yo@fullstack.to) 。我很乐意帮助你！

## 链接

*   [NestJS 文档](https://docs.nestjs.com/)
*   [Auth0](https://auth0.com)
*   [Auth0 管理 API 文档](https://auth0.com/docs/api/management/v2)

* * *

如果你喜欢我的内容，你可能想在 Twitter 上关注我？！ [@fullstack_to](https://twitter.com/fullstack_to)

[un plash](https://unsplash.com/photos/8yYAaguVDgY)上 [Jason Blackeye](https://unsplash.com/@jeisblack) 的封面图片