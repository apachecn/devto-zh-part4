# 可怕的 mongoDB sintaxe

> 原文：<https://dev.to/habutre/the-terrifying-mongodb-sintaxe-1gk7>

有经验的软件开发人员的主要职责是帮助他人，并利用获得的经验来解决教学、学习和分享中的问题。

我想分享一个简单的事情，让我思考复杂性和权衡。我的一位同事向我寻求关于 MongoDB 聚合查询的帮助:

> 既然我使用的是 NoSQL 数据库，我该如何汇总我需要的数据？
> 
> 我能利用我以前的 SQL 知识来实现它吗？

嗯，我认为事情可以更简单，但不是。看看下面的例子。

我的应用程序中发生了一系列事件，其中某个特定事件的发生频率似乎比预期的要高。我想查一下那件事多久发生一次。

关于 mongo:

```
mongo-client-shell> db.orders.aggregate([ 
  { $match: {"event": "order.event.OrderPickedEvent"}} ,
  { $group : { _id: {"event": "$event", "orderNumber": '$orderNumber'}, count: { $sum: 1 } } }, 
  { $sort: {count: -1}}
], 
{allowDiskUse:true}
) 
```

关于 Postgresql:

```
psql> select count(*) as total 
  from order 
  where event = "order.event.OrderPickedEvent" 
  group by orderNumber 
  order by total desc; 
```

我脑子里的问题是:为什么 Mongo 把事情弄得如此怪异，以至于不能说复杂？

这对读者来说是不自然的。我们总是在谈论代码的清晰性、可读性以及让事情变得更简单。但似乎大玩家不是这样想的。

我希望这篇小文章能帮助其他人，也许能让该工具的供应商变得敏感，让生活变得更容易*。

*   我找到了一个 SQL 翻译器，可以帮助 NoSQL 解决这个问题[https://studio3t.com/](https://studio3t.com/)