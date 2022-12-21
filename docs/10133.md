# 用 Prisma2 和 Photon 制作 GraphQL API

> 原文：<https://dev.to/itminds/making-a-graphql-api-with-prisma2-and-photon-262a>

Prisma2 是一个 ORM/数据库迁移工具，它创建了一个从服务器到数据库的 GraphQL api。
这个博客将涵盖 Prisma2 的一些概念，但它不是一个“入门”教程，这可以参考 Prisma 自己的文档:[入门](https://github.com/prisma/prisma2/blob/master/docs/getting-started.md)

使用 prisma2 和 Photon 创建服务器时，我们需要:

1.  以 prisma 语法声明数据库模式。
2.  创建将向客户端公开的应用程序模式。

在我们进入 graphql 开发之前，我们需要理解什么是 GraphQL 类型和查询，因为我们将在服务器上实现它们。

一个 graphql 类型

```
type  User  {  id:  ID!  name:  String  purchases:  [Transaction!]  sells:  [Transaction!]  username:  String!  }  type  Query  {  user(userId:  ID!):  User  } 
```

对类型
的查询

```
query  MyNewUserQuery($id:  ID!)  {  user(userId:  $id)  {  name  id  username  }  } 
```

点击了解有关 GraphQL [的更多信息](https://graphql.org/)

## 棱镜模式

Prisma 模式很像用
[SDL](https://www.prisma.io/blog/graphql-sdl-schema-definition-language-6755bcb9ce51) 编写的，除了一些微小的语法变化，比如字段和字段类型之间的:。

```
model User {
  id        String        @default(cuid()) @id @unique
  username  String        @unique
  details   UserDetails
  purchases Transaction[] @relation(name: "TransactionToUser2")
  sells     Transaction[] @relation(name: "TransactionToUser")
}

model Transaction {
  id     String @default(cuid()) @id @unique
  item   Item
  price  Int
  seller User   @relation(name: "TransactionToUser")
  buyer  User   @relation(name: "TransactionToUser2")
} 
```

这里需要注意一些事情，每个模型的 id 都有注释和一个 id，这意味着它将在数据库中被索引。Prisma 给出了一组默认函数，在这种情况下，这些函数可以创建一个默认 UID `cuid()`。
在上面的模型中，用户可能有一组购买和一组销售。这意味着将有两个不同的字段引用同一个模型(对于事务模型中的用户也是如此)，但是我们可以命名关系，这确保了正确的字段映射到正确的用户。

因为 Prisma2 正在创建数据库模型，所以通过简单地注释每一个都有一个相反类型的数组来建立多对多关系几乎是微不足道的:

```
model User {
  id           String         @default(cuid()) @id @unique
  transactions Transaction[] 

}

model Transaction {
  id     String @default(cuid()) @id @unique
  users  User[]
} 
```

当模式被写入数据库时，Prisma2 将生成一个 Photon API，它可以被实例化并用于查询数据库。

如果我要返回所有用户的数组，我可以使用 photon like `photon.users.findMany()`返回所有用户的列表。

在 findMany 函数中，如果我只想要用户名为`'Yes'`的用户(我不知道为什么)，可以给一个限制对象来限制返回的数据。`ctx.photon.users.findMany({ where: { username: 'Yes' } })`，
在 findMany 的参数中生成的一般类型是:

```
export declare type FindManyUserArgs = {
    select?: UserSelect;
    include?: UserInclude;
    where?: UserWhereInput;
    orderBy?: UserOrderByInput;
    skip?: number;
    after?: string;
    before?: string;
    first?: number;
    last?: number;
}; 
```

选择和包含用于将您想要的字段从类型中列入黑名单/白名单。
注意，这些类型是为了匹配 prisma 模式而生成的，所以所有生成的类型可能都不匹配这个类型，但是它描绘了一幅这个 API 是如何经过深思熟虑的美丽图画。

### 分页

如上所示，你可以做两种不同类型的分页，即“跳转”和“先后”分页，这是一种基于光标的分页。

Prisma2 通过@prisma/nexus 将界限推得更远，当您创建应用程序模式时，它将从生成的类型中为您提供智能感知。(这是从数据库中选择要向客户端公开的数据的部分)

```
// src/types/User.ts
import { objectType } from '@prisma/nexus';

export const User = objectType({
  name: 'User',
  definition(t) {
    t.model.id();
    t.field('computedField', {
      type: 'String',
      resolve: () => 'some computed value',
    });
    t.model.purchases({ type: 'Transaction' });
    t.model.sells({ type: 'Transaction' });
    t.model.username();
  },
}); 
```

如果 name 属性与模型名称匹配，Prisma2 生成的类型会将 schematypes 添加到 objectType 全局变量中。
所以`t.model.purchases({type: 'Transaction'})`是从上面 prisma 模式中显示的用户模型中安全地推断出来的。
然而，如果我想要一个计算字段，我可以毫不费力地添加它。

上面的代码将生成一个 graphql API，您可以像 blogpost 开头所示的那样进行查询。

谈满一圈=)