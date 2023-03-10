# MongoDB 入门指南。聚合()

> 原文：<https://dev.to/mandaputtra/beginner-guide-on-mongodb-aggregate-337g>

这篇文章是我今年在 NoSQL 数据库上的旅程，所以这是从初学者到初学者的指南。

MongoDB 或 NoSQL 已经成为趋势，但出于某种原因，我仍然喜欢 SQL 数据库。然而，MongoDB 有一个很棒的查询语法(尽管在性能等方面)。)那我承认我爱上了那个。我一直在使用 MongoDB，现在我想分享一些使用 aggregate 技巧！MongoDB 中的强大功能。

哦，是的，你会对语法感到困惑，它看起来像这样:

```
.aggregate([{{{{{{{{{ }}}}}}}}}]) // I love aggregate, better use your space and get ready 
```

Enter fullscreen mode Exit fullscreen mode

我们将学习如何使用`$facet`、`$match`、`$unwind`、`$project`和`$group`。这里先提供一些数据:

```
//  Just  a  simple  chat  schema  here  for  a  real-world  example  //  RoomID  for  chat  room  since  it  chatting  room  //  Clients  contains  all  clients  on  a  room  ~  {  "_id"  :  ObjectId("5d1449db6f934a2926c175d1"),  "clients"  :  [  "rooberduck@mail.com",  "sillybilly@mail.com"  ],  "roomId"  :  1,  "message"  :  [  {  "time"  :  ISODate("2019-06-27T04:44:49.528Z"),  "status"  :  "SEND",  "text"  :  "Hello!",  "sender"  :  "sillybilly@mail.com",  "receiver"  :  "rooberduck@mail.com",  "_id"  :  ObjectId("5d1449db6f934a2926c175d2")  },  {  "time"  :  ISODate("2019-06-27T04:54:49.528Z"),  "status"  :  "READ",  "text"  :  "Hello Wow Are You?",  "sender"  :  "rooberduck@mail.com",  "receiver"  :  "sillybilly@mail.com",  "_id"  :  ObjectId("5d1449ff6f934a2926c175d3")  }  ],  "__v"  :  0  ....  } 
```

Enter fullscreen mode Exit fullscreen mode

### 第一种情况:获取一个用户的所有消息，并返回他的最后一条消息

好的，为了做到这一点，你可以使用`$match: { clients: "sillybilly@mail.com" }`，但是如何只显示最后一条消息，你可以使用`$project`

在这里，project 只获取你所请求的字段，使用 aggregate 就像使用*流*或*管道*一样，你应该关心它的每一步。

```
db.chat.aggregate([
  { $match: { clients: "sillybilly@mail.com" } },
  { $project: { _id: "$roomId", message: { $slice: ["$message", -1] } } }
])

// result
[{
    "_id" : 1,
    "message" : [
        {
            "time" : ISODate("2019-06-27T04:54:49.528Z"),
            "status" : "SEND",
            "text" : "Hello!",
            "sender" : "sillybilly@mail.com",
            "receiver" : "rooberduck@mail.com",
            "_id" : ObjectId("5d1449ff6f934a2926c175d3")
        }
    ]
}, ... more other result ] 
```

Enter fullscreen mode Exit fullscreen mode

所以`$project`获取先前结果的字段或结果并投影它，您可以通过使用“$”获得结果的先前值，只要它与结果字段的匹配。

“但是数据怎么会没问题呢？你只有一条信息，为什么你需要一个数组？我不需要那个！以及为什么我应该去`message`字段取数组[0]并得到 sender”——一个健康的前端开发者。

### 第二种情况:得到前端开发者想要的东西

假设您只选择 sillybilly 作为发送者，即使用您的应用程序的发送者，而在**客户端阵列**上的其他用户是其他用户。`$unwind`去营救。

```
db.getCollection('chats').aggregate([
    { $match: { clients: "sillybilly@mail.com" } },
    { $unwind: "$clients" }
]) 
```

Enter fullscreen mode Exit fullscreen mode

查询结果将给出匹配文档的解构数组元素。

```
[{ foo: [bar, baz] }] // not using unwind
[{ foo: bar }, {foo: baz}] // using unwind $foo
// get that? so now every documents are decoupled 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们可以得到接收者的名字:

```
db.getCollection('chats').aggregate([
    { $match: { clients: "sillybilly@mail.com" } },
    { $unwind: "$clients" },
    { $match: { clients: { $ne: 'sillybilly@mail.com' } } },
    { $project: {
      _id: "$roomId",
      receiver: "$clients",
      message: { $slice: ["$message", -1]
      }
    }}
])

// result

[{
    "_id" : 1,
    "receiver" : "rooberduck@mail.com",
    "message" : [
        {
            "time" : ISODate("2019-06-27T04:54:49.528Z"),
            "status" : "SEND",
            "text" : "Hello!",
            "sender" : "sillybilly@mail.com",
            "reciever" : "rooberduck@mail.com",
            "_id" : ObjectId("5d1449ff6f934a2926c175d3")
        }
    ]
} ... another result] 
```

Enter fullscreen mode Exit fullscreen mode

好的，现在再给前端开发者发一条消息。但是等一下，你再次看到设计，它得到了未读信息徽章...你应该数一数。嗯。你又坐下来思考..

### 第三种情况:对 messages 对象数组内的未读消息进行计数

你已经感觉到它的语法了吗？:lol:。

你已经`$unwind`了客户，你应该再`$unwind`一遍信息吗？是的。这就像是在用流工作，所以你应该遵循你创造的模式。

因为在`$project`中使用`$sum`需要大量的`$cond`，所以可以使用`$group`来简化查询。`$group`有很多[累加器](https://docs.mongodb.com/manual/reference/operator/aggregation/group/#accumulator-operator)操作符，可以用来检测`$last` / `$first`元素。

```
//  unwind it again
db.getCollection('chats').aggregate([
    { $match: { clients: "sillybilly@mail.com" } },
    { $unwind: "$clients" },
    { $match: { clients: { $ne: 'sillybilly@mail.com' } } },
    { $unwind: "$message" }, // messages decoupled her
    { $match: "$message.status": 'SEND' }
    { $group: {
      _id: '$_id', // we group it by _id
      roomId: { $first: '$roomId' }, // accumulator must be used otherwise error will thrown
      receiver: { $first: '$clients' },
      unread: { $sum: 1 },
      message: { $last: '$message.text' },
      time: { $last: '$message.time' }
    }
  }
])

// result
[{
    "_id" : ObjectId("5d1449ff6f934a2926c175d3"),
    "receiver" : "rooberduck@mail.com",
    "unread": 1,
    "message" : "Hello!",
    "time": ISODate("2019-06-27T04:54:49.528Z")
}... another result] 
```

Enter fullscreen mode Exit fullscreen mode

> 我认为这个查询执行得不好，想象一下，如果您有 1000 条消息，您将展开 1000 条消息，这会影响性能吗？如果你知道最好的方法，请在下面评论😁

现在，您从数据库中获得了一个漂亮的响应，您在设计 UI 中唯一缺少的就是分页！

### 第 4 种情况:分页。使用$facet 聚合()

关于`$facet`的文档是这样说的:“在同一组输入文档的单个阶段中处理多个聚合管道”。

这意味着我们可以像使用`$unwind`一样在`$facet`中使用聚合。但是我们不需要再次展开，要通过`$facet`进行分页，你可以使用这个查询:

```
db.getCollection('chats').aggregate([
    { $match: { clients: "sillybilly@mail.com" } },
    { $unwind: "$clients" },
    { $match: { clients: { $ne: 'sillybilly@mail.com' } } },
    { $unwind: "$message" },
    { $match: "$message.status": 'SEND' }
    { $group: {
        _id: '$_id',
        roomId: { $first: '$roomId' },
        receiver: { $first: '$clients' },
        unread: { $sum: 1 },
        message: { $last: '$message.text' },
        time: { $last: '$message.time' }
      }
    },
    {
      $facet: {
          metadata: [{ $count: 'total' }, { $addFields: { page: 1 } }],
          data: [
            { $skip: 0 },
            { $limit: 15 }
          ]
        }
    }
  ])

// result: now you got a paginated response
[{
    "metadata": [ { $count: 'total' }, { page: 1 } ],
    "data": [
      {
        "_id" : ObjectId("5d1449ff6f934a2926c175d3"),
        "receiver" : "rooberduck@mail.com",
        "unread": 1,
        "message" : "Hello!",
        "time": ISODate("2019-06-27T04:54:49.528Z")
      }
    ]
}... another result] 
```

Enter fullscreen mode Exit fullscreen mode

就这样，你得到了漂亮的响应 API，现在你来到前端开发人员面前说，你完成了，他/她接受你的 API 响应。

下一篇文章再见，祝你今天愉快:)

随时欢迎反馈。