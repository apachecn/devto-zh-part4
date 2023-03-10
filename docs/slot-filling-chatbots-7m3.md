# 填隙聊天机器人

> 原文：<https://dev.to/mcsh/slot-filling-chatbots-7m3>

假设我经营一家运输公司。我需要知道你需要什么样的车，什么时候需要，你的货物有多重，你的目的地和出发点。

很久以前，我会把这份表格打印在纸上，交给你填写，或者自己通过电话填写。然后我会计算价格并派人去处理。

几年前，我会把它转换成某种计算机程序，要么是基于网络的，要么是本地二进制的，我会让用户填写，或者通过对话自己填写。

现在，由于目前对社交媒体和即时消息的兴趣，我可以在其中一个上部署我的应用程序，让客户与它聊天来下订单。让我们看看会怎样。

## 场景 1:基于状态的聊天机器人

基于状态的聊天机器人很简单，在对话的每一点上，它都希望从用户那里得到一些东西。考虑下面的对话:

```
User: Hi!
Bot's State: conversation_start
Bot: Hi, welcome to my shop! Do you want to put an order?

User: Yes
Bot's State: order_weight
Bot: Ok, how heavy is your cargo?

User: Around 400 Kg
Bot's State: order_dest
Bot: And where is your destination?

... 
```

你可以想象会发生什么。如果用户遵从机器人的要求，这将很好地工作。现在考虑这个:

```
User: Hi! I have 400Kg cargo that I want to deliver to X.
Bot: Hi, welcome to my shop! Do you want to put an order?

User: Ummm, yes, I have a 400kg cargo for X.
Bot: Ok, how heavy is your cargo? 
```

行动组。进展不太顺利。

## 场景二:填槽聊天机器人

填隙聊天机器人与常规的基于状态的聊天机器人没有什么不同。也许唯一真正的区别是，它使用某种形式的 NLU 来理解用户在说什么。比方说，用户在第一条消息中提供她的货物重量。填隙聊天机器人会跳过那一步，因为它已经知道了重量。所以第二次对话会进行得更顺利。

在接下来的帖子中，我会试着写一个关于写一个填充聊天机器人的教程