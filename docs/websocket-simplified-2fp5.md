# 简化的 WebSocket

> 原文：<https://dev.to/iamshadmirza/websocket-simplified-2fp5>

这是我将要写的 WebSocket 系列的第一篇文章，目标是尽可能用最简单的方式解释事情。让我们直接开始吧。

# 什么是 WebSocket？

WebSocket 允许用户向服务器发送和接收消息。
所以基本上，这是**客户端**和**服务器**之间的一种通信方式。
先来了解一下这个**通信**我们一会儿再回到 WebSocket。

## 客户端和服务器端

Web 浏览器(客户端)和服务器通过 TCP/IP 进行通信。超文本传输协议(HTTP)是位于之上的标准应用协议*，支持请求(来自网络浏览器)及其响应(来自服务器)。*

### 这是怎么回事？

让我们通过这些简单的步骤:-

1.  客户端(浏览器)向服务器发送请求。
2.  建立了连接。
3.  服务器发回响应。
4.  客户端收到响应。
5.  连接已关闭。

这就是客户端和服务器之间通信的基本方式。现在仔细看看第五步。

> 连接已关闭。

HTTP 请求已经达到了它的目的，不再需要它，因此连接被关闭。

#### 服务器要给客户端发消息怎么办？

必须成功建立连接才能开始通信。这里的解决方案是客户端必须发送另一个请求来建立连接并接收消息。

#### 客户端怎么会知道服务器要发送消息？

考虑这个例子:-

*客户快要饿死了，在网上订购了一些食物。他每秒发出一个请求来检查订单是否准备好了。*

> 0 秒:食物准备好了吗？(客户端)
> 
> 0 秒:不，等等。
> (服务员)
> 1 秒:菜好了吗？
> (客户端)
> 1 秒:不，等等。(服务器)
> 
> 2 秒:菜好了吗？(客户端)
> 
> 2 秒:不，等等。(服务器)
> 
> 3 秒:菜好了吗？是的，先生，这是您点的菜。(服务器)

这就是你所谓的 **HTTP 轮询**。

> 客户端反复向服务器发出请求，并检查是否有消息要接收。

如你所见，这不是很有效率。我们正在使用不必要的资源，失败请求的数量也很麻烦。

#### 有什么办法可以克服这个问题？

是的，有一种轮询技术的变体用来克服这种不足，它被称为 ***长轮询*** 。

> 长轮询基本上包括向服务器发出一个 HTTP 请求，然后保持连接打开，以允许服务器稍后响应(由服务器决定)。

考虑上面例子的**长轮询**版本:-

> 0 秒:食物准备好了吗？3 秒钟:是的，先生，这是您点的菜。(服务器)

耶，问题解决了。

不完全是。虽然长轮询有效，但它在 CPU、内存和带宽方面非常昂贵(*，因为我们在保持连接打开的过程中阻塞了资源*)。

现在该怎么办？看起来事情正在失去控制。让我们回到我们的救世主: **WebSocket** 。

## 为什么是 WebSocket？

如您所见，为了模拟客户机和服务器之间的实时通信，轮询和长轮询都是非常昂贵的选择。

> 这种性能瓶颈是您希望使用 WebSocket 的原因。

WebSockets 不需要你发送请求来响应。它们允许*双向*数据流，所以你只需要监听任何数据。

> 你可以只听服务器，当它可用时，它会给你发一条信息。

让我们看看 WebSocket 的性能方面。

### 资源消耗

下面的图表显示了在一个相对常见的用例中 WebSockets 与长轮询之间的带宽消耗差异:

[![Resource graph](img/bfce996321527cd2760c071e7c8497b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hQIAPLUp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/websocket-basics/resource_graph.png) 

差异是巨大的(对于相对较高数量的请求)。

### 速度

下面是一秒钟内为每个连接处理 1 个、10 个和 50 个请求的结果:

[![Speed Graph 1](img/1641f67292e85c760f6d0c37138723b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LeQqJMzT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/websocket-basics/speed_graph.png) 

正如您所看到的，使用 Socket.io 为每个连接处理一个请求要慢 50%，因为必须首先建立连接。这种开销较小，但对于十个请求来说仍然很明显。对于来自同一连接的 50 个请求，Socket.io 已经快了 50%。为了更好地了解峰值吞吐量，我们将查看每个连接的请求数量更大(500、1000 和 2000)的基准:

[![Speed Graph 2](img/ab8503dd7c29cbce5120a379dd2c6f39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ek8H1fUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/websocket-basics/speed_graph2.png)

这里可以看到，HTTP 基准测试的峰值约为每秒 950 个请求，而 Socket.io 每秒处理约 3900 个请求。有效吧？

> 注意:Socket.io 是一个用于实时 web 应用程序的 JavaScript 库。它在内部实现 WebSocket。可以把它看作 WebSocket 的包装器，它提供了更多的特性*(本系列的下一篇博文将详细解释 socket . io)*。

## web socket 是如何工作的？

这些是建立 WebSocket 连接所涉及的步骤。

1.  客户端(浏览器)向服务器发送一个 HTTP 请求。
2.  通过 HTTP 协议建立连接。
3.  如果服务器支持 WebSocket 协议，它同意升级连接。这叫做握手。
4.  既然握手已经完成，初始的 HTTP 连接就被一个使用相同的底层 TCP/IP 协议的 WebSocket 连接所取代。
5.  此时，数据可以在客户机和服务器之间自由地来回流动。

# 咱们码吧

我们将创建两个文件:一个服务器和一个客户端。

首先创建一个简单的`<html>`文档，名为`client.html`，包含一个`<script>`标签。让我们看看它看起来怎么样

### Client.html

```
<html>

<script>
    // Our code goes here
</script>

<body>
    <h1>This is a client page</h1>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在创建另一个文件`server.js`。现在导入 HTTP 模块并创建一个服务器。让它听`port 8000`。

这将作为一个简单的`http`服务器监听`port 8000`。让我们也来看看:-

### Server.js

```
//importing http module
const http = require('http');

//creating a http server
const server = http.createServer((req, res) => {
    res.end("I am connected");
});

//making it listen to port 8000
server.listen(8000); 
```

Enter fullscreen mode Exit fullscreen mode

> 运行命令`node server.js`开始监听`port 8000`。
> 
> *注:——你可以随意选择任何一个端口，我只是选择了 8000，没有具体原因。*

客户端和服务器的基本设置已经完成。很简单，对吧？现在让我们来看看好东西。

### 客户端设置

要构造一个 **WebSocket** ，使用返回 WebSocket 对象的`WebSocket()`构造函数。这个对象提供了创建和管理到**服务器**的 WebSocket 连接的 API。

简而言之，这个 websocket 对象将帮助我们建立与服务器的连接，并创建双向数据流，即*从两端发送和接收数据*。

让我们看看如何:-

```
<html>

<script>
    //calling the constructor which gives us the websocket object: ws
    let ws = new WebSocket('url'); 
</script>

<body>
    <h1>This is a client page</h1>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

`WebSocket`构造函数需要一个 URL 来监听。在我们的例子中是`'ws://localhost:8000'`,因为那是我们的服务器运行的地方。

现在，这可能与你所习惯的有些不同。我们没有使用`HTTP`协议，我们使用`WebSocket`协议。这将告诉客户端**‘嘿，我们正在使用 websocket 协议’**，因此是`'ws://'`而不是`'http://'`。够简单吗？现在让我们在`server.js`中实际创建一个 WebSocket 服务器。

### 服务器设置

我们将需要一个第三方模块`ws`在我们的节点服务器中使用设置`WebSocket`服务器。

首先，我们将导入`ws`模块。然后我们将创建一个 websocket 服务器，并把监听`port 8000`的`HTTP`服务器交给它。

> HTTP 服务器正在侦听端口 8000，WebSocket 服务器正在侦听此 HTTP 服务器。基本上，就是听听者的话。

现在我们的 websocket 正在观察`port 8000`上的流量。这意味着一旦客户端可用，它将尝试建立连接。我们的`server.js`文件将如下所示:-

```
const http = require('http');
//importing ws module
const websocket = require('ws');

const server = http.createServer((req, res) => {
    res.end("I am connected");
});
//creating websocket server
const wss = new websocket.Server({ server });

server.listen(8000); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们之前讨论过的:

> `WebSocket()`构造函数返回一个 websocket 对象，提供 API 来创建和管理与**服务器**的 WebSocket 连接。

在这里，`wss`对象会帮助我们监听某件事情发生时发出的`Event`。如连接建立或握手完成或连接关闭等。

我们来看看怎么听消息:-

```
const http = require('http');
const websocket = require('ws');

const server = http.createServer((req, res) => {
    res.end("I am connected");
});
const wss = new websocket.Server({ server });
//calling a method 'on' which is available on websocket object
wss.on('headers', (headers, req) => {
    //logging the header
    console.log(headers);
});

server.listen(8000); 
```

Enter fullscreen mode Exit fullscreen mode

方法`'on'`需要两个参数:事件名和回调。事件名称识别要监听/发出哪个事件，回调指定如何处理它。这里，我们只是记录了`headers`事件。让我们看看我们得到了什么:-

[![header log](img/da235c96afc42df298203335f5d02a35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lI3-NSfq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/websocket-basics/header.png) 

这是我们的 HTTP 头，我希望你对它好奇，因为这正是幕后发生的事情。让我们分解一下，以便更好地理解。

*   你会注意到的第一件事是我们得到了状态码`101`。你可能见过`200`、`201`、`404`状态码，但这个看起来不一样。`101`实际上是交换协议的状态码。上面写着**“嘿，我想升级”**。
*   第二行显示升级信息。它指定它想要升级到`websocket`协议。
*   这实际上是握手过程中发生的事情。浏览器使用`HTTP`连接来建立使用`HTTP/1.1`协议的连接，然后将其`Upgrade`到`websocket`协议。

现在这将更有意义。

> 作为握手的一部分，在将响应头写入套接字之前发出事件。这允许您在发送前检查/修改标题。
> 
> 这意味着你可以修改标题，接受，拒绝或其他任何你喜欢的。默认情况下，它接受请求。

类似地，我们可以再添加一个事件`connection`，当握手完成时发出这个事件。成功建立连接后，我们将向客户端发送一条消息。让我们看看如何:-

```
const http = require('http');
const websocket = require('ws');

const server = http.createServer((req, res) => {
    res.end("I am connected");
});
const wss = new websocket.Server({ server });

wss.on('headers', (headers, req) => {
    //console.log(headers); Not logging the header anymore
});

//Event: 'connection'
wss.on('connection', (ws, req) => {
    ws.send('This is a message from server, connection is established');
    //receive the message from client on Event: 'message'
    ws.on('message', (msg) => {
        console.log(msg);
    });
});

server.listen(8000); 
```

Enter fullscreen mode Exit fullscreen mode

我们也在监听来自客户端的事件`message`。让我们创建:-

```
<html>

<script>
    let ws = new WebSocket('url'); 
    //logging the websocket property properties
    console.log(ws);
    //sending a message when connection opens
    ws.onopen = (event) => ws.send("This is a message from client");
    //receiving the message from server
    ws.onmessage = (message) => console.log(message);
</script>

<body>
    <h1>This is a client page</h1>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

这是它在浏览器中的样子:-

[![browser screenshot](img/4c3eacb2e1d1551c4a8000c69dcd3923.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nt-2lQKN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/websocket-basics/client.png) 

第一个日志是列出 websocket 对象所有属性的`WebSocket`，第二个日志是具有`data`属性的`MessageEvent`。如果你仔细看，你会看到我们从服务器上得到我们的信息。

服务器日志会是这样的:-

[!['server log'](img/83f745dae6876b1258fb1eb8970f24b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K21Q8YQp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/BlogsByShad/master/blogs/websocket-basics/server.png)

我们正确地得到了客户端的消息。这标志着我们的连接成功建立。干杯！

## 结论

总而言之，让我们回顾一下我们所学的内容

*   我们已经介绍了 HTTP 服务器如何工作，什么是轮询，长轮询。
*   什么是 WebSockets，为什么我们需要它们。
*   我们讨论了它们在幕后是如何工作的，并对标题有了更好的理解。
*   我们创建了自己的客户端和服务器，并成功地建立了它们之间的连接。

这是 WebSockets 及其工作原理的基础。这个系列的下一篇文章将会更详细地介绍`socket.io`和工作。我们还将看到，当只有本地的`WebSocket()`运行良好时，我们到底为什么需要`socket.io`。当我们可以很好地成功发送和接收消息时，为什么要使用笨重臃肿的库呢？

如果你觉得这篇文章有帮助，请分享，并关注下一篇。

沙德。

## 引用

*   Web socket-Web API | MDN:[https://developer.mozilla.org/en-US/docs/Web/API/WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
*   `ws`节点服务器|文档的模块:[https://github . com/web sockets/ws/blob/HEAD/doc/ws . MD # event-headers](https://github.com/websockets/ws/blob/HEAD/doc/ws.md#event-headers)