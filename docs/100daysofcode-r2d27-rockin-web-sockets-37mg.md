# 100DaysOfCode R2D27 - Rockin' (Web)套接字！

> 原文：<https://dev.to/lindakatcodes/100daysofcode-r2d27-rockin-web-sockets-37mg>

这是一篇来自我个人博客的交叉帖子。欢迎下方评论！

我开始了我[节点课程](https://www.udemy.com/the-complete-nodejs-developer-course-2/)的最后一个项目——一个聊天 app！我昨天做了基础视频-安装库和新的文件/目录。

今晚，我开始研究实际的代码。对于这个项目，我们使用的主库是 [Socket.io](https://socket.io) ，到目前为止它真的很酷！这让我对 web sockets 有了一点了解，这是一个我以前听说过但没有真正注意过的术语。

根据我的理解，web 套接字是 HTTP 请求/响应模式的升级。它们在一个服务器和一个客户端(或多个客户端)之间创建连接！)，并保持该连接打开，以便双方可以比使用标准请求更快地相互发送数据。我不完全明白**它是如何做到这一点的——只是它确实做到了。**

Socket.io 构建于此之上，提供了一些很好的 API 魔法，使得使用服务器/客户端连接变得更加容易。对我来说，真正酷的是它以发送(发射)自定义事件为中心——所以你可以随意命名你的事件！然后，您可以用它发送任何您想要的数据，并设置另一端监听自定义事件。(它也有一些内置功能，比如连接和断开。)

让我分享一个简单的例子，我们一直在研究这个问题。这将向我们显示一个基本的计数器值——然后，一旦单击一个按钮，它将更新值并向我们显示新值。

我们从 index.js 文件(我们的服务器文件)开始，它正在运行一个 Express 应用程序。(您看到的 io 字是设置到我们的 Express 服务器的变量，socket.io 正在控制这个服务器。)在这个文件中，我们为一个计数器设置了一个变量，然后当一个新的客户端连接进来时，我们发送(发出)一个定制的命名事件，以及 count 变量的当前值。

```
let count = 0;
io.on('connection', (socket) => { 
  socket.emit('countUpdated', count); 
}) 
```

Enter fullscreen mode Exit fullscreen mode

socket.emit 短语是关键——它将数据发送到我们的客户端/浏览器端。

在我们的客户机上，我们设置了一个监听器函数，监听我们刚刚创建的自定义事件名称。当它检测到正在发送的事件时，它会获取与之一起发送的数据，并对其进行处理(在本例中，只是将其记录到控制台)。(我应该注意，在这个例子中，socket 也是我们的文件中定义的一个变量，它访问我们正在运行的服务器。)

```
socket.on('countUpdated', (count) => { 
  console.log('The count has been updated!', count) 
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们第一次连接时，我们的服务器正在调用一个事件并发送 count 的初始值，我们的客户端正在拾取该事件并将该值记录到控制台。

然后，如果我们在客户端单击一个按钮(在 html 文件中设置),我们希望增加 count 的值。因此，我们在客户机文件中获取按钮点击事件，并向服务器发送一个事件，以表示按钮被点击。再说一次，我们活动的名称是自定义的——它可以被称为任何东西！只需要两边匹配就行了。

```
document.querySelector('#btnIncrease').addEventListener('click', () => { 
  socket.emit('increment') 
}) 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们的服务器检测到这个事件，我们就想增加 count 的值，并用新的计数值发回第一个事件。(这个小调用发生在我们之前进行的`io.on('connection')`调用中。)

```
socket.on('increment', () => { 
  count++; 
  io.emit('countUpdated', count) 
}) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在，当我们第一次打开浏览器访问我们的站点时，我们会收到一条带有初始计数值的消息。每当我们点击页面上的按钮时，我们的客户端检测到按钮点击，向我们的服务器发送一个事件，计数器加 1，服务器将新值发送回客户端！

你可能已经注意到，有时我用`socket.emit`，有时用`io.emit`。到目前为止，课程中介绍了 3 个基本概念/使用案例:

*   socket . emit-socket 关键字绑定到一个特定的客户端，所以它只向那个特定的客户端发送事件。
*   **io.emit** -使用 io 关键字将事件发送到所有活动的、连接的客户端
*   **socket.broadcast.emit** -这不在上面的例子中，但这将向所有连接的客户端**发送一个事件，除了**您所在的特定客户端(我们在课程中使用它用于当用户登录或注销时-您希望其他人看到新用户加入或离开，但您自己不需要看到您何时加入聊天！)

今晚真的很有趣，为我们的应用构建核心功能。app 两端收发信息用的代码这么少！这让我很困惑。🤯我很高兴能回去继续这个项目！我已经有了如何使用这些信息的想法。😊