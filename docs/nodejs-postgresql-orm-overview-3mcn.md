# NodeJS & PostgreSQL: ORM 概述

> 原文：<https://dev.to/miku86/nodejs-postgresql-orm-overview-3mcn>

## [T1】简介](#intro)

在上两部分中，我们学习了如何将数据库连接到服务器:

*   [无 ORM](https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-without-an-orm-10o0)
*   [与 ORM](https://dev.to/miku86/nodejs-postgresql-how-to-connect-our-database-to-our-simple-express-server-with-an-orm-gcm)

## ORM(对象关系映射器)

### ORM 是做什么的？

简而言之，ORM 是服务器和数据库之间的一层。服务器与 ORM 对话，ORM 与数据库对话。
ORM 创建映射到关系数据的对象。
它处理你的查询，所以你不用写原生 SQL，你可以用你的应用语言查询数据库。

### ORM 列表:

*   sequelize : Postgres，MySQL，MariaDB，SQLite，微软 SQL Server
*   [TypeORM](https://www.npmjs.com/package/typeorm) : Postgres，MySQL，MariaDB，SQLite，微软 SQL Server，Oracle，sql.js，CockroachDB
*   [异议](https://www.npmjs.com/package/objection) : Postgres，MySQL，MariaDB，SQLite，微软 SQL Server，Oracle，亚马逊红移

[NPM 趋势比较](https://www.npmtrends.com/objection-vs-sequelize-vs-typeorm)

如果使用`MongoDB`，可以使用 ODM，例如 [`mongoose`](https://www.npmjs.com/package/mongoose) 。

### 优点

*   您不必学习/了解/编写 SQL，因为 ORM 会处理它
*   改变你的数据库方言会更容易
*   您的应用程序不容易受到 SQL 注入的攻击

### 弊

*   你必须学会 ORM
*   一个额外的抽象层降低了速度(理论上)

## 进一步阅读

[wiki:form](https://en.wikipedia.org/wiki/Object-relational_mapping)
[测序](https://www.npmjs.com/package/sequelize)
[【type】](https://www.npmjs.com/package/typeorm)[【反对】](https://www.npmjs.com/package/objection)

## 提问

*   您是否使用 ORM/ODM(例如 Mongoose)？哪一个？为什么？