# NestJS 中的 TypeORM 连接

> 原文：<https://dev.to/dwipr/typeorm-connection-in-nestjs-5f4a>

*印尼语帖子，[NestJS](https://antaraksi.com/tutorial-nestjs-untuk-backend-rest-api-bag-2-koneksi-ke-basis-data-mysql/)类型连接。*

软件可以有保存数据到数据库的功能。为了保存数据，它通常使用 MySQL、SQLite 或 MongoDB 等软件，并使用 ORM(对象关系映射)进行通信。

NestJS 有三种 ORM 支持，即 TypeORM、Sequelize 和 Mongoose。在本教程中，我将只使用 TypeORM。与其他 ORM 相比，NestJS 中的 TypeORM 的优势在于 TypeORM 和 Nest 框架都是使用 Typescript 语言构建的，因此可以很好地集成。

下面是使用 TypeORM 将 MySQL 数据库连接到 Nest 框架应用程序的步骤:

## 安装依赖项。

首先，我们将安装 TypeORM 依赖项。我们将使用 MySQL 作为数据库。

```
$ npm install --save @nestjs/typeorm typeorm mysql 
```

## 在 MySQL 中创建数据库。

我们现在将创建一个数据库，它将连接到 MySQL 中的嵌套应用程序。

```
CREATE DATABASE hello; 
```

## 进行连接配置。

现在，我们将在`AppModule`中进行连接配置，以连接到我们的 MySQL `hello`数据库。从 Nest 这里导入并使用`TypeOrmModule`。

```
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'root',
      database: 'hello',
      entities: [__dirname + '/**/*.entity{.ts,.js}']
    }),
  ],
})
export class AppModule {} 
```

完成后，我们的应用程序现在可以连接到我们的`hello`数据库了。用户名和密码配置可能不同，请根据您的需要进行调整。

## 附加:TypeORM CLI。

TypeORM 具有 CLI 功能，可用于创建实体、迁移等。如果我们像上面第 3 条那样进行连接配置，我们就不能使用 TypeORM 的 CLI。

作为替代，我们可以进行基于 json 的配置。

在这个项目的根目录下创建一个名为`ormconfig.json`的文件，在配置中填入:

```
{
  "type": "mysql",
  "host": "localhost",
  "port": 3306,
  "username": "root",
  "password": "root",
  "database": "hello",
  "entities": ["src/**/*.entity{.ts,.js}"],
} 
```

同样，用户名和密码配置可以不同，调整它以适合你的。

现在，删除`TypeOrmModule.forRoot()`中的连接配置，使其只调用`TypeOrmModule.forRoot()`

```
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [TypeOrmModule.forRoot()],
})
export class AppModule {} 
```

完成，现在我们可以使用 TypeORM CLI 了，因为我们的应用程序现在从`ormconfig.json`读取连接配置。