# 向 Azure 事件中心发送结构化消息

> 原文：<https://dev.to/dev3l/sending-structured-messages-to-azure-event-hubs-3gi0>

基于使用 Python 的 Azure Event Hubs 在[数据摄取中发送和接收消息，让我们探索如何使用](https://dev.to/dev3l/data-ingestion-with-azure-event-hubs-using-python-2dda)[结构化数据](https://www.datamation.com/big-data/structured-data.html)使它们变得有意义。

# 心中有了结局

当我们的产品进入现实世界时，我们相信我们知道是真的东西通常会消失。如果我们的提取和转换步骤紧密耦合，那么我们很难有效地重新处理数据。

### 保存原始数据

只要有可能，保留原始数据允许重新处理它，而不必重新捕获整个数据集！此时，请克制住转换数据的冲动。这使得我们能够分离我们的架构。

### Meta-Data

提取一些元数据是必要的，比如一个*记录身份*，但是我们必须确保我们有一个*唯一分隔符*来标记消息。我最近用`|~|`作为我的摄取管道之一。此外，*记录类型*将帮助我们实现一个命令设计模式来处理每个单独的事件。可以添加额外的属性来帮助数据的转换，例如*配置文件名*，如果消息是相关的。

# 用 Python 构建结构化消息

给定我们的元数据属性:

*   *记录身份* = > `id`
*   *记录类型* = > `type`
*   *档案名称* = > `profile`

给定我们的唯一分隔符:

*   `|~|`

这是一个简短的 Python 方法，它断言消息将以一种格式发送，这种格式易于我们转换成对系统其余部分有意义的输入。

```
def build_message(profile: str, message_type: str,
                  event_id: str, message: str,
                  *, delimiter: str = data_delimiter):

    structured_message = \
        f'profile:{profile}{delimiter}' \
        f'type:{message_type}{delimiter}' \
        f'id:{event_id}{delimiter}' \
        f'message:{message}'

    return structured_message

# => profile:dev3l|~|type:tweet|~|id:1|~|message:hello, world 
```

# 结论

有了一个结构良好的消息，我们可以很容易地实时处理我们的事件，或者在时机成熟时处理整个事件流！在我们的下一个冒险中，我们将手动处理我们的一些消息，并将它们存储在一个持久存储中，本地存储在 Mongo DB 中，稍后使用 [Azure Cosmos DB](https://azure.microsoft.com/en-us/free/cosmos-db) 存储在云中。