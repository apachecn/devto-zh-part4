# 使用 socket.io 的请求-响应模式，我使用的是反模式吗？

> 原文：<https://dev.to/hongduc/request-response-pattern-with-socket-io-am-i-using-anti-pattern-4m8c>

你好，我正在使用 socket.io 构建一个聊天应用程序。但是有东西击中了我。现在，我正在用 socket.io 重新实现请求-响应模式，因为每次客户端发送消息时，我都需要知道消息是否发送或超时。但是这样做的时候，我想为什么不用 http 来发送消息呢？但是我记得我想避免请求开销。

我在这里使用了反模式吗？我做事情的方式不对吗？谢谢你