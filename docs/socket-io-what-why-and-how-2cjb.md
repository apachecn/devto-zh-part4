# 插座。IO -什么，为什么和如何？

> 原文：<https://dev.to/iamshadmirza/socket-io-what-why-and-how-2cjb>

这是系列 **WebSocket 简化版**的第二篇博文。如果你没有看过第一部分，看看这里的。我坚持要你看完第一部分，以便更好地理解这一部分。同样，目标是让事情尽可能简单。所以我会用不同的方式来探讨这个话题。我们开始吧。

## 为什么插口。木卫一？

在前一篇文章中，我们成功地构建了基本的 WebSocket 服务器和客户端，并且运行得相当好。所以，你可能想知道为什么要用 socket.io？如果没有它也能正常工作，为什么还要使用一个臃肿的库呢？让我们考虑这个场景:-

假设你的老板告诉你在网站上增加实时交流，你决定使用本地 WebSocket 而不是任何库。

你对 WebSockets 进行了正确编码，它工作正常*除了*测试显示有一些问题。因为某些原因，它每次都不能正常工作。

经过一番调试，你发现当这个人在*防火墙*或者*杀毒*后面的时候就不行了。所以你要知道

> 在有防火墙和防病毒软件的情况下，WebSocket 不能理想地工作。

您还发现，如果客户端位于代理或负载平衡器之后，那么 websocket 也不会工作。

> WebSocket 在代理和负载平衡器存在的情况下也不能理想地工作。

这意味着你必须自己处理所有这些问题。您工作了几个小时并添加了额外的代码来解决所有这些问题，您的 WebSocket 连接终于正常工作了。看看我们到目前为止所做的直观表示:-

[!['demo1'](img/0c8973bb766a8d3faa77b0eb9b17498b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z_9S9khg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/socketio-why-and-how/demo1.jpg)

现在，假设服务器由于某种原因关闭，连接断开。在这种情况下，浏览器现在处于理想状态，不做任何事情，因为握手对于建立连接是必要的。所以你现在还有一件事要做

> Websocket 应该在由于服务器故障或其他原因断开连接的情况下自动重新连接。

所以现在你添加了更多的代码来解决这个问题。很好，你是一个非常好的开发者。

[!['you're awesome'](img/626fd685591dcbd3474ec6e6a684b0fb.png)](https://i.giphy.com/media/5C0b4tU550kNGRmJrU/giphy.gif) 

你的老板欣赏你的努力，但现在他告诉你也要添加对图像(斑点)的支持。

> 注意:到目前为止，我们发送的是 JSON 数据(纯文本)。

不仅如此，您还必须添加聊天室进行群聊，多个端点以便您可以连接多个 WebSocket。此外，可能有一些旧的浏览器不支持 WebSocket，所以你也必须注意这一点。

这个现在看起来很多了吧？

[!['please save me'](img/5556ac997b757f2327aa5acd23fcbfeb.png)](https://i.giphy.com/media/3o7TKEP6YngkCKFofC/giphy.gif) 

再多一些代码和努力，你也攻克了这些。现在你的 WebSocket 实现看起来像这样:-

[!['demo2'](img/2a5937a86b717c96ecdbab55161dbb0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6g-3sZO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/socketio-why-and-how/demo2.jpg)

你猜怎么着，这整个集合实际上就是你所说的 Socket.io。查看下面的图片，只是为了让事情变得非常清楚。

[!['demo3'](img/7f3025250c6734c2a5421453e23d4fb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0HEbkPEh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/socketio-why-and-how/demo3.jpg)

## 什么是插座。木卫一？

*插座。io* 在幕后使用 WebSocket，处理所有这些问题，这样我们就不用自己处理了。它只是原生 WebSocket 上的一个包装器，提供了一堆其他很酷的特性，使我们作为开发人员的工作变得容易。

[!['phew'](img/88dfce072a5897945312c7dc274fe570.png)](https://i.giphy.com/media/JMV7IKoqzxlrW/giphy.gif)

### 按定义

> 插座。IO 是一个用于实时 web 应用程序的 JavaScript 库。它支持 web 客户端和服务器之间的实时双向通信。

插座。IO 主要使用带有 ***轮询的 WebSocket 协议作为后备选项*** ，同时提供相同的接口。虽然它可以简单地作为 WebSocket 的包装器使用，但是它 ***提供了更多的特性*** ，包括向多个套接字广播、存储与每个客户端相关的数据以及异步 I/o

> 注意:再读一遍，特别注意粗体字。我们已经在“为什么”部分讨论过了。现在说得通了吧？

我们将了解 Socket 的核心特性。本系列的第三部分。让我们将 WebSocket 客户端和服务器文件(在[第一部分](https://dev.to/iamshadmirza/websocket-simplified-2fp5)中创建)转换成使用 Socket 。木卫一快。

## 如何？

我假设你已经阅读了[第一部分](https://dev.to/iamshadmirza/websocket-simplified-2fp5)，并且知道本地 WebSockets 的工作原理。让我们快速升级到插座 .io.

> 注释代码是 WebSocket 用法。我加了比较。

### 服务器

[!['server'](img/551bebdbfabb95362b0518289a51ef70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5DphykU6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/socketio-why-and-how/server.png)

**解释**

1.  我们不是从“ws”导入 websocket，而是从“socket.io”库中导入 socketio。
2.  创建一个服务器并把它交给 socketio 而不是 websocket。
3.  我们现在对“io”对象使用“emit”函数，而不是“send”。唯一的区别是我们添加了*事件名*作为第一个参数。我们将使用这个*事件名称*在客户端检索消息。
4.  方法“on”仍然有效，唯一的区别是增加了*事件名称*。够简单吗？

### 客户端

[!['client'](img/ca8ec2b3379f60ba1e5a980f66e93831.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HxifL1lH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/socketio-why-and-how/client.png)

**解释**

1.  正在导入“socket.io”库，因为它不是由 JavaScript 现成提供的。这将公开“io”命名空间。
2.  为 io 连接提供 url。(与 WebSocket 用法不同，我们没有使用新键盘，因为它是可选的。你想不想用由你决定。)
3.  “发送”再次被“发出”代替，我们添加了事件名称(检查服务器部分的第 21 行)。
4.  方法“on”仍然是相同的，只是增加了*事件名称*(检查服务器部分的第 20 行)。有道理？

干杯！我们已经创建了简单的套接字。IO 服务器和客户端。

[!['spongebob](img/ca3df204ade1690f7a4311a530d5b3e8.png)T4】](https://i.giphy.com/media/26u4lOMA8JKSnL9Uk/giphy.gif)

我尽量让事情变得简单。我们将深入探讨 Socket 。本系列的第三部分。让我知道这是否有帮助。

沙德

## 引用

*   插座。IO 服务器 API-[https://socket.io/docs/server-api/](https://socket.io/docs/server-api/)T3】
*   插座。IO 客户端 API-[https://socket.io/docs/client-api/](https://socket.io/docs/client-api/)T3】