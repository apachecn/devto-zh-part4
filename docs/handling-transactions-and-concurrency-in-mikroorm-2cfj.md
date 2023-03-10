# 在 MikroORM 中处理事务和并发

> 原文：<https://dev.to/b4nan/handling-transactions-and-concurrency-in-mikroorm-2cfj>

## 等等，什么？MikroORM？

如果你从未听说过 [MikroORM](https://github.com/B4nan/mikro-orm) ，它是一个带有工作单元和身份映射的类型化数据映射 ORM。目前支持 MongoDB，MySQL，PostgreSQL，SQLite 驱动。

[![](img/536f59c58dac0bfe29152bfc96c89710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SL7QlMOB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A_WcU6mEj971G2xrIkr7duQ.png)

你可以在这里或者[浏览文档](https://b4nan.github.io/mikro-orm/)阅读完整的[介绍文章。该项目正在积极开发中，因此请务必查看](https://dev.to/b4nan/introducing-mikroorm-typescript-data-mapper-orm-with-identity-map-pc8)[变更日志](https://github.com/B4nan/mikro-orm/blob/master/CHANGELOG.md)。

> MikroORM 深受 ORM[学说](https://www.doctrine-project.org/)的启发。这篇文章受到了[教义文档](https://www.doctrine-project.org/projects/doctrine-orm/en/latest/reference/transactions-and-concurrency.html)的高度启发，因为 MikroORM 中的行为几乎是相同的。这个主题的一般解释的所有功劳归于他们！

## 关于坚持的注意事项

为了理解 MikroORM 中持久化是如何工作的，我们应该首先描述两种方法:`em.persist()`和`em.flush()`。

`em.persist(entity, flush?: boolean)`用于标记新的实体，以便将来持久化。它将使实体由给定的`EntityManager`管理，一旦`flush`被调用，它将被写入数据库。第二个布尔参数可用于立即调用`flush`。其默认值可通过`autoFlush`选项进行配置。

> `autoFlush`的默认值目前设置为`true`，在即将到来的主要版本中会有所改变。鼓励用户将`autoFlush`设置为`false`，或者使用`em.persistLater()`(等于`em.persist(entity, false)`)和`em.persistAndFlush()`方法。本文中每次提到坚持，都是考虑到将`autoFlush`设置为`false`。

为了理解`flush`，让我们首先定义什么是受管实体:如果一个实体从数据库中取出(通过`em.find()`、`em.findOne()`或通过其他受管实体)或通过`em.persist()`注册为新的，那么这个实体就是受管的。

`em.flush()`将遍历所有受管实体，计算适当的变更集，并执行相应的数据库查询。当一个从数据库加载的实体被自动管理时，你不需要在这些实体上调用`persist`，并且`flush`足以更新它们。

[![](img/d351284c25c709964b2ff22b5db54f91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tpJsvcXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AEo5JP9abOfPV24Uf.jpg)

## 交易分界

事务界定是定义事务边界的任务。在很大程度上，MikroORM 已经为您处理了适当的事务划分:所有的写操作(插入/更新/删除)都被排队，直到调用`em.flush()`将所有这些更改封装在一个事务中。然而，MikroORM 也允许(并鼓励)您自己接管和控制事务划分。

### 方法一:含蓄地

第一种方法是使用 MikroORM `EntityManager`提供的隐式事务处理。给定以下代码片段，没有任何显式的事务划分: