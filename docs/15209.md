# 使用手柄进行服务器端渲染的 NestJS 认证

> 原文：<https://dev.to/boobo94/nestjs-authentication-for-server-side-rendering-with-handlebars-2jk0>

*用手柄进行服务器端渲染的 [NestJS 认证的帖子最早出现在](https://boobo94.xyz/tutorials/nestjs-authentication/) [boobo94.xyz](https://boobo94.xyz) 上。因此，如果你想阅读更多类似的文章，请[订阅我的时事通讯](https://boobo94.xyz/#colophon)或关注我这里的*

下面是我如何用手柄管理服务器端渲染的 [NestJS](https://nestjs.com/) 认证。最近，我不得不为一个项目实现一个简单的认证系统，用 NestJS 和用[手柄](http://handlebarsjs.com/installation.html)渲染的模板登录。

下面你可以看到我想在下面几行中展示的文件夹结构:

src

【main . ts】

【app . module . ts】

【auth】

【auth . controller . ts】【t】

### 授权模块. ts

```
import { Module } from '@nestjs/common'; import { AuthService } from './auth.service'; import { UsersService } from '../users/users.service'; import { DatabaseModule } from '../database/database.module'; import { AuthController } from './auth.controller'; @Module({ imports: [DatabaseModule,], providers: [UsersService, AuthService,], controllers: [AuthController], }) export class AuthModule { } 
```

Enter fullscreen mode Exit fullscreen mode

### 授权控制器. ts

```
import { Controller, Get, UseGuards, Render, Post, Body, Res, Req } from '@nestjs/common'; import { AuthService } from './auth.service'; import { UserDto } from '../users/user.dto'; import { ValidateRequest } from '../validation/validation'; import { Response, Request } from 'express'; @Controller('auth') export class AuthController { constructor(private readonly authService: AuthService) { } @Get('login') @Render('auth/login') async loginView() { } @Post('login') async login(@Body() userDto: UserDto, @Req() request, @Res() response: Response) { const payload = await ValidateRequest(UserDto, userDto) if (payload) { return response.render('auth/login', { ...payload, }) } try { const user = await this.authService.login(userDto); // store the username in session request.session.username = user.username; } catch (err) { return response.render('auth/login', { data: userDto, globalError: err.message, }) } return response.redirect(`/`) } @Get('logout') logout(@Req() request, @Res() response: Response) { request.session.destroy(); return response.redirect('/auth/login') } } 
```

Enter fullscreen mode Exit fullscreen mode

### 认证中间件. ts

```
import { Injectable, NestMiddleware, UnauthorizedException } from '@nestjs/common'; import { Response } from 'express'; @Injectable() export class AuthMiddleware implements NestMiddleware { use(req, res: Response, next: Function) { if (req.session &amp;&amp; req.session.username) return next(); throw new UnauthorizedException(); } } 
```

Enter fullscreen mode Exit fullscreen mode

### 授权服务. ts

```
import { Injectable } from '@nestjs/common'; import { UsersService } from '../users/users.service'; import { UserDto } from 'src/users/user.dto'; const bcrypt = require('bcrypt'); @Injectable() export class AuthService { constructor( private readonly usersService: UsersService, ) { } async login(payload: UserDto): Promise\<UserDto\> { const user = await this.usersService.findByUsername(payload.username); if (!user) throw new Error('User is not registered') const match = await bcrypt.compare(payload.password, user.password); if (!match) throw new Error('Invalid username or password') return user } } 
```

Enter fullscreen mode Exit fullscreen mode

### 应用模块. ts

```
@Module({ imports: [DatabaseModule, AuthModule, UsersModule,], controllers: [AppController], providers: [AppService], }) export class AppModule implements NestModule { // add authorization middleware for the following routes configure(consumer: MiddlewareConsumer) { consumer .apply(AuthMiddleware) .forRoutes( 'demo', 'demo/create', 'demo/edit/:id', 'demo/delete/:id', // add here all the routes that you want to be protected ); } } 
```

Enter fullscreen mode Exit fullscreen mode

### user.dto.ts

```
import { IsNotEmpty } from "class-validator"; export class UserDto { id?: number; @IsNotEmpty() username: string; @IsNotEmpty() password: String; } 
```

Enter fullscreen mode Exit fullscreen mode

## [T1】user . service . ts](#userservicets)

```
import { Injectable, Inject } from '@nestjs/common'; import { Op } from 'sequelize'; import { newTransaction } from '../../../src/database'; import { UserDto } from './user.dto'; @Injectable() export class UsersService { private model constructor( @Inject('SEQUELIZE') private readonly sequelize, ) { this.model = this.sequelize.models.users } async findByUsername(username: string): Promise\<UserDto\> { return await this.model.findOne({ where: { username: { [Op.eq]: username } } }) } } 
```

Enter fullscreen mode Exit fullscreen mode

### 验证过滤器. ts

```
import { ExceptionFilter, Catch, ArgumentsHost, HttpException, HttpStatus } from '@nestjs/common'; import { Response } from 'express'; @Catch(HttpException) export class HttpExceptionFilter implements ExceptionFilter { catch(exception: HttpException, host: ArgumentsHost) { if (exception.getStatus() === HttpStatus.UNAUTHORIZED) { const ctx = host.switchToHttp(); const response = ctx.getResponse\<Response\>(); return response.redirect('/auth/login') } } } 
```

Enter fullscreen mode Exit fullscreen mode

### main.ts

```
require('dotenv').config({ path: '../.env' }) // initialize dotenv import { NestFactory } from '@nestjs/core'; import { NestExpressApplication } from '@nestjs/platform-express'; import { join } from 'path'; import { AppModule } from './app.module'; import { HttpExceptionFilter } from './auth/auth.filters'; import \* as session from 'express-session' const fs = require('fs'); const hbs = require('hbs'); async function bootstrap() { const app = await NestFactory.create\<NestExpressApplication\>(AppModule); // session app.use(session({ secret: process.env.SESSION\_KEY, cookie: { maxAge: 86400000, // 24h } })) app.useGlobalFilters(new HttpExceptionFilter()); // initialize handlebars js app.useStaticAssets(join(\_\_dirname, '..', 'public')); app.setBaseViewsDir(join(\_\_dirname, '..', 'views')); app.setViewEngine('hbs'); // load partials loadComponents('templates'); loadComponents('auth'); require('handlebars-form-helpers').register(hbs.handlebars); await app.listen(3000); } bootstrap(); function loadComponents(pathName: string) { const partialsDir = \_\_dirname + `/../views/${pathName}`; const filenames = fs.readdirSync(partialsDir); filenames.forEach(function (filename) { var matches = /^([^.]+).hbs$/.exec(filename); if (!matches) { return; } const name = `${pathName}_${matches[1]}`; const template = fs.readFileSync(partialsDir + '/' + filename, 'utf8'); hbs.registerPartial(name, template); }); } 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】auth/log in . HBS](#authloginhbs)

```
{{#\> templates\_auth\_layout titlePage="Login" }} {{#form ""}} \<div class="form-group"\> {{label\_validation 'username' 'Username \*' errors class="control-label"}} {{input 'username' data.username class="form-control"}} \<small class="form-text text-danger"\>{{#field\_errors "username" errors}}{{this}}{{/field\_errors}}\</small\> \</div\> \<div class="form-group"\> {{label\_validation 'password' 'Password \*' errors class="control-label"}} {{input 'password' data.password class="form-control"}} \<small class="form-text text-danger"\>{{#field\_errors "password" errors}}{{this}}{{/field\_errors}}\</small\> \</div\> {{submit 'submit' 'Login' class="btn btn-primary"}} {{/form}} {{/templates\_auth\_layout}} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这是有用的。如果你对我如何实现认证有任何疑问，请在评论中告诉我。

*用手柄进行服务器端渲染的 [NestJS 认证的帖子最早出现在](https://boobo94.xyz/tutorials/nestjs-authentication/) [boobo94.xyz](https://boobo94.xyz) 上。因此，如果你想阅读更多类似的文章，请[订阅我的时事通讯](https://boobo94.xyz/#colophon)或关注我这里的*