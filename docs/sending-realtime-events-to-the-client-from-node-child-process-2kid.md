# 从节点子进程向客户端发送实时事件

> 原文：<https://dev.to/gate3/sending-realtime-events-to-the-client-from-node-child-process-2kid>

因此，我最近不得不在现有的应用程序中添加一个新功能。这项新功能做了一些数据密集型的工作，比如处理大型文档，这些文档的内容要保存到数据库中。

自然，我将文件中的数据排队，并在一个分叉的子进程中使用队列，然后将信息保存到子进程中的数据库中。为了发送关于处理状态的进度报告，我决定使用 socketio 向客户端触发事件。这种方法给我带来了几个问题，一个是处理速度很快，socketio 实例不能捕获大多数事件，另一个问题是如何在父节点和子节点之间使用相同的 socketio 实例。

我后来接受的方法是使用 Redis Pub/Sub 从子进程中触发事件，监听主进程并将所述事件发送给客户端。这种方法行之有效，伸缩性好，性能非常好。

# 现在为一些代码

我将假设您有一个现有的 nodejs 应用程序，并且数据已经排队。我们需要安装以下内容

*   Redis Nodejs 客户端(我使用[https://www . npmj . com/package/redis](https://www.npmjs.com/package/redis)
*   套接字 io

两者都可以使用 npm 进行安装。`npm i -S socket.io redis`

# RabbitMqHelper

虽然这超出了本文的范围，但是我编写了一个 RabbitMq 助手，在我的应用程序中使用。