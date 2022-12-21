# NestJS:SequilizeJS 入门

> 原文：<https://dev.to/hzburki/nestjs-getting-started-with-sequilizejs-21p>

## 前言

> 我来自 Laravel，它有无数的内置特性，我开始讨厌转移到 NodeJS。设置错误处理、日志记录、依赖注入等。不是我想要的。谢天谢地，我找到了 NestJS，它的开箱即用的架构和很棒的文档让我又有了家的感觉。
> 
> 这是 NestJS 漫长技术旅程中的一小步。

SequelizeJS 是一种 ORM，它提供了到关系数据库的连接，如 MySQL、PostgreSQL 和 MSSQL。对于本文，我将使用托管在 [RemoteMySQL](https://remotemysql.com/) 上的 MySQL，但是您可以使用任何您喜欢的关系数据库。

## 入门

假设您已经准备好了一个 nest 项目。我们将从安装以下依赖项开始。

```
npm install --save sequelize sequelize-typescript mysql2
npm install --save-dev @types/sequelize 
```

首先，我们将把连接细节传递给 SequelizeJS。我们可以通过创建数据库模块和提供程序来实现这一点。

```
nest generate module database
nest generate provider database/database.providers 
```

这是我们将实体模型添加到 SequelizeJS 的地方。我现在正在添加模型(尽管它们已经创建好了)，但是你可以以后再做。