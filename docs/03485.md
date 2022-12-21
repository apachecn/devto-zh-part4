# 如何在 NestJS 上管理环境变量

> 原文：<https://dev.to/rubiin/how-to-manage-environment-variables-on-nestjs-30ge>

环境变量允许我们独立于代码库来管理应用程序的配置。分离配置使我们的应用程序更容易部署在不同的环境中(开发、测试、生产)。此外，大型应用程序往往有许多环境变量。

如果您正在使用 nestjs 进行某个项目，并且希望以更有效的方式配置您的应用程序秘密和凭证，那么您来对地方了。在本文中，我想分享一些初始配置和使用环境变量的技巧。

## 描述

一个常见的应用级工具是`nestjs-easyconfig`，它允许我们从一个名为。环境[生产|开发|测试]。
[Nestjs-easyconfig](https://github.com/rubiin/nestjs-easyconfig) 从您的`.env`(包装 dotenv 模块)⚙️加载配置🔥

## 安装

```
$ npm install nestjs-easyconfig
$ yarn add nestjs-easyconfig 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

### 提供配置文件(基本):

```
import  { Module }  from  '@nestjs/common';
import { EasyconfigModule } from  'nestjs-easyconfig';

@Module({
 imports:  [EasyconfigModule.register({path: './config/.env'})],
})
export  class  AppModule  {} 
```

Enter fullscreen mode Exit fullscreen mode

### 提供配置文件，安全设置为真:

```
import  { Module }  from  '@nestjs/common';
import { EasyconfigModule } from  'nestjs-easyconfig';

@Module({
 imports:  [EasyconfigModule.register({path: './config/.env', safe: true})],
})
export  class  AppModule  {} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，safe 设置为 false。当 safe 设置为`true`时，模块将提供的 env
文件与样本 env 文件进行比较，以查找丢失的键。如果使用了. env.sample 中但不在环境中的任何键，控制台会立即报告。

`Note`:要使用它，一个示例 env 文件`.env.sample`应该放在根目录下

### 未提供配置文件:

```
import  { Module }  from  '@nestjs/common';
import { EasyconfigModule } from  'nestjs-easyconfig';

@Module({
 imports:  [EasyconfigModule.register({})],
})
export  class  AppModule  {} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，您必须传入 **NODE_ENV** 值，要读取的`.env`文件将相应地被确定。
从`.env.[development|test|production][.local]`文件
中加载环境变量，例如 **NODE_ENV=dev** 会让应用读取`.env.dev`

> 注意:在。env 文件也必须在根文件夹中

## 保持联系

*   作者- [鲁宾班达里](https://github.com/rubiin)
*   dev․to-[@鲁宾说](https://dev.to/rubinsays)
*   不和谐- [@rubin#1186](https://discordapp.com/)

## 执照

这个包是麻省理工学院许可的。

## 支撑在横梁上

嘿伙计！帮我做几个🍻！

[![Beerpay](img/2cb34104c340f0d94e2c37798f38c1c8.png) ](https://beerpay.io/rubiin/nestjs-easyconfig) [ ![Beerpay](img/fa00053d1a31255f1c0e5cfce024d5d9.png)](https://beerpay.io/rubiin/nestjs-easyconfig?focus=wish)