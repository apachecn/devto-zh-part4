# 如何使用 NestJS 和 GraphQL API 示例构建您的 JavaScript API 第 1/2 部分。

> 原文：<https://dev.to/itminds/how-to-architecture-your-javascript-api-using-nestjs-with-a-graphql-api-example-part-1-2-2jcb>

传统的 NodeJS 和 Express 后端在某种程度上是相当简单的。您有一个 RouteHandler 和一些根据请求返回一些数据的函数。虽然节点服务器很简单，但是我们经常害怕在维护或更新这些项目时打开一个旧的项目。

Node 并没有给出一个具体的指导，告诉我们如何构建 API，因此我们看到了一堆执行各种操作的捷径，比如:数据访问、验证、日志记录等等。虽然情况并非总是如此，但我们确实偶尔会看到它们。

以及这些关于后端 API 应该如何构建以及逻辑是否应该分离的“有趣”解释。我们确实看到结构化的 API，其中数据访问和业务逻辑被分离到类似 MVC 架构的不同模块中。

尽管我很喜欢 Node 和 Express 提供给我们的自由度。我相信这种自由带来的问题是我们打开节点应用程序时如此恐惧的主要原因。

在这一系列的博客文章中，我们将看看 NestJS 如何指导我们如何构建我们的 Node Express (Fastify)等等...)带有 GraphQL API 示例的应用程序和 API。希望随着我们对 NestJS 的深入挖掘，你会经历和我一样的顿悟。

在本系列的第一部分中，我们将设置项目并讨论 NestJS CLI 为我们提供了哪些现成的东西。我们将看看如何设置一个简单的 MongoDB 并构建一个 Mongoose 模式和模型，这比使用一些 JSON 对象更有趣。

在第二部分中，我们将开始用模式优先的方法创建一个 GraphQL API，以及 NestJS 如何使用依赖注入和装饰器来帮助构建应用程序。我们将看看 NestJS 引入的一些怪癖和一些很酷的东西。

# 什么是 NestJS？

NestJS 在 Node 之上提供了一个抽象，并利用了 Express(至少 pr。默认)。NestJS 提供了一个开箱即用的应用程序架构，这让我们想起了很多构建 Angular 应用程序的方式。有了像模块、服务、拦截器和防护器这样的东西，我们可以清楚地看到它们的灵感来自哪里。这种架构考虑到了可伸缩性、易维护性和可测试性，如 [Angular](https://angular.io/) 中的应用程序。将您的验证、业务逻辑以及数据访问分离到不同的模块和服务中，提供了一个简洁且可测试的应用程序和 API。但是说得够多了，让我们开始深入研究吧。

# 我们开始吧

首先，我们下载 NestJS CLI 并创建一个新项目:

`$ npm i -g @nestjs/cli`
T1】

NestJS 使用了 TypeScript 的强大功能。如果您正在使用 TypeScript 编写应用程序，您可能已经知道为什么这是一个开箱即用的好东西。

在 *src/main.ts* 中，我们看到了一个函数，它的作用类似于我们应用程序的入口点。就像《快递》里一样。我们创建一个应用程序并监听某个端口。要运行您的应用程序，请使用:

`$ npm run start:dev`

通过在浏览器中转至 *localhost:3000* 或使用您的 favorit API 测试工具来测试 API。

# 项目结构

如果我们打开 *src* 我们会看到三种不同类型的文件:*控制器*、*模块*和*服务*。控制器是我们定义不同端点的地方。在文件中，我们看到我们可以注释函数，如 Get、Post、Put 等。由于我们将使用 GraphQL，这是不相关的。我们稍后将创建解析器，它将充当控制器。

**Service** :是一个可注入的组件，我们用它来获取数据到我们的控制器。

# MongoDB 和 Mongoose

为了使用我们自己的数据库，我们使用 MongoDB 和 Mongoose:

`$ npm install --save mongoose`
T1】

我喜欢把我的数据库配置放在一个数据库文件夹中( *src/database* )。创建一个我们可以用来注入模型类的模块。在一个经典的 Mongo 设置中，这将使每个依赖连接的类等待，直到一个承诺被解决。

```
// products.module.ts

import { Module } from '@nestjs/common';
import { databaseProviders } from './database.providers';

@Module({
  providers: [...databaseProviders],
  exports: [...databaseProviders],
})
export class DatabaseModule {} 
```

创建数据库提供程序。在这里，我们可以指定连接选项作为可选参数，例如: *{useNewUrlParser: true}* 。

```
// database.providers.ts

import * as mongoose from 'mongoose';
import { KEYS } from '@/config/config.constants';

export const DATABASE_CONNECTION = 'DATABASE_CONNECTION';

export const databaseProviders = [
  {
    provide: DATABASE_CONNECTION,
    useFactory: (): Promise<typeof mongoose> =>
      mongoose.connect(KEYS.mongodb_connection_uri, { useNewUrlParser: true }),
  },
]; 
```

现在，我们几乎准备好连接到我们的数据库，我们现在需要的是定义一些模型，我们想保存在我们的数据库中。

# 创建产品模型

在数据库文件夹中创建一个新的*模式*文件夹。在 Schemas 文件夹中创建一个 products-folder 并创建两个文件:*products . schema . ts*&*products . interface . ts*。接口文件如下:

```
// products.interface.ts

import { Document } from 'mongoose';

export interface Product {
  title: string;
  brand: string;
  currentPrice: number;
}

export interface ProductDocument extends Document, Product {} 
```

模式如下:

```
// products.schema.ts

import { ObjectId } from 'mongodb';
import { Model, Schema } from 'mongoose';
import { ProductDocument } from './products.interface';

export const ProductSchema = new Schema({
  _id: { type: ObjectId, auto: true },
  title: String,
  brand: String,
  currentPrice: Number,
});

export type ProductModel = Model<ProductDocument>; 
```

这是我们目前产品模型。我们现在将创建一个模块，我们可以定义我们的类型，查询和突变。但是首先我们需要创建一个 ProductProvider:

```
// products.providers.ts

import { Connection } from 'mongoose';
import { ProductSchema } from './products.schema';
import { DATABASE_CONNECTION } from '@/database/database.providers';

export const PRODUCT_MODEL = 'PRODUCT_MODEL';

export const productsProviders = [
  {
    provide: PRODUCT_MODEL,
    useFactory: (connection: Connection) =>
      connection.model('Product', ProductSchema),
    inject: [DATABASE_CONNECTION],
  },
]; 
```

# 产品模块

使用 NestJS CLI 创建一个新模块:

`$ nest g module products`

添加*产品供应商*和*数据库模块* :

```
// products.module.ts

import { Module } from '@nestjs/common';
import { DatabaseModule } from '@/database/database.module';
import { productsProviders } from '@/database/schemas/products/products.providers';

@Module({
  imports: [DatabaseModule],
  providers: [...productsProviders],
})
export class ProductsModule {} 
```

将**产品模块**导入到**应用模块**中，并添加到*导入*中。现在我们终于可以开始创建我们的 API 了！所以请继续关注这个系列的第二部分，我们终于可以开始编码了:)
这将在下周五发布！