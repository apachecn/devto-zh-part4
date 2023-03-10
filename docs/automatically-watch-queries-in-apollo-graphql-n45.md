# 自动观察 Apollo GraphQL 中的查询

> 原文：<https://dev.to/bdbch/automatically-watch-queries-in-apollo-graphql-n45>

我刚刚使用了一个从 Apollo GraphQL 服务器获取数据的钩子，但是我注意到当当前订阅没有打开时，我的数据不会在本地缓存中更新。

这是我之前的代码:

```
const chatId = '123456';

client.query({
  query: GET_CHAT_MESSAGES,
  variables: {
    chatId,
  }
}).then(res => {
  // do stuff here
}) 
```

该查询在运行一次后不会更新，因为 Apollo 将所有查询保存在本地，而不知道潜在的更改。

当发生变化时，聊天视图仍然会显示旧的状态，即使我的订阅会重写缓存。

我必须做的是使用`client.watchQuery`而不是`client.query`。watchQuery 允许您通过突变来观察查询的潜在变化，并在需要时重新运行查询。它还允许您使用“缓存和网络”提取策略。

现在我的代码是这样的:

```
const chatId = '123456';

const querySubscription = client.watchQuery({
  query: GET_CHAT_MESSAGES,
  variables: {
    chatId,
  },
  fetchPolicy: 'cache-and-network'
}).subscribe({
  next: ({ data }) => { // do stuff },
  error: (e) => console.error(e)
}) 
```

只要确保使用`querySubscription.unsubscribe()`断开不再需要的订阅即可。