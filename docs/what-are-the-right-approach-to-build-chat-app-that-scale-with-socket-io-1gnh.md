# 构建可随 socket.io 扩展的聊天应用的正确方法有哪些？

> 原文：<https://dev.to/mandaputtra/what-are-the-right-approach-to-build-chat-app-that-scale-with-socket-io-1gnh>

嗨，我有一点思考问题，想弄清楚聊天应用程序到底有多好。

在这种情况下，我使用 socket.io，我想普通的 Websockets 也使用类似的方法...

## 发送消息给用户，私人消息

这里有很多例子，包括保存 websockets 的 socket.id，或者 sockets.io id。

但是什么是正确的方法呢？

io 有房间功能，如果有其他人聊天，我可以创建新的独特的房间吗？

Websockets 可以使用 redis pub/sub 我应该创建动态 redis pub/sub 来发送消息吗？

如果我有 100.000++用户连接到动态发布/订阅或套接字，这些方法可以吗？

有一些工具叫做 rabbitMQ，一些人在建立聊天应用或实时应用时推荐它，它实际上是做什么的？它是简化应用程序和易于扩展的应用程序？

什么是消息代理？为什么 keep 被推荐用于构建实时应用？