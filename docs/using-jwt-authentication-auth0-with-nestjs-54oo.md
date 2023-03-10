# 对 NestJS 使用 JWT 身份验证(Auth0)🔐

> 原文：<https://dev.to/matthias/using-jwt-authentication-auth0-with-nestjs-54oo>

我试图用 [Auth0](//https:/auth0.com) 来保护我的基于 [NestJS](https://nestjs.com) 的 API。

我现在做的事情感觉不太对，🥴.

我试图使用来自 Auth0 的 [NestJS 文档](https://docs.nestjs.com/techniques/authentication)和[用 Nest.js 开发后端 API，但是我不知道我在做什么。](https://auth0.com/blog/full-stack-typescript-apps-part-1-developing-backend-apis-with-nestjs/)

你们中有人已经解决这个问题了吗？你能提供一些好的资源或建议吗？

下面是我的策略实现:

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
        jwksUri: '${DOMAIN}/.well-known/jwks.json'
      }),
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      audience: 'http://localhost:3000',
      issuer: '${DOMAIN}'
    });
  }

  async validate(payload: any, done: VerifiedCallback) {
    if (!payload) {
      done(new UnauthorizedException(), false);
    }

    return done(null, payload);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在控制器中，我使用了一个*身份保护* :

```
@UseGuards(AuthGuard('jwt')) 
```

Enter fullscreen mode Exit fullscreen mode

我还想从 Auth0 检索经过身份验证的用户的元数据。有人知道怎么做吗？