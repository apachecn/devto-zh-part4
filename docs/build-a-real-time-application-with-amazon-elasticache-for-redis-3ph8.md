# 用 Amazon ElastiCache 为 Redis 构建一个实时应用程序

> 原文：<https://dev.to/giladmaayan/build-a-real-time-application-with-amazon-elasticache-for-redis-3ph8>

在本文中，我们将向您介绍在 Nodejs 后端中组合 Socket.io 和 Redis 所需的步骤，并将其与 React 前端连接起来。然后，我们将看到在合适的平台上部署应用程序的步骤。那么，我们将创造什么？Redis 帮助下的基本聊天应用。用户可以设置他们的用户名，然后发送消息。

这种模式非常适合许多应用程序，包括游戏、聊天、交易等。这篇文章并不排斥 React，其基本原则应该很容易转化为 Angular 和 Vue 等其他框架。但是，您应该熟悉 React、Express 和 Redis 的概念才能开始。

## 结构

要构建像聊天应用程序那样的实时应用程序，我们需要建立一个通道，客户端可以通过该通道发送消息，并且消息可以与组中的其他参与者共享。我们将使用 Websockets 来管理客户机和服务器之间的持久 TCP 通信。但是，通信将使用发布-订阅模式(PubSub)来实现。

在这里，消息被发送到一个集中的主题通道。感兴趣的人可以订阅这个频道以获得更新通知。这种模式将发布者和订阅者分离，因此订阅者集可以在发布者不知情的情况下增长或收缩。Redis 是一个快速、开源、内存中的数据存储和缓存，支持 PubSub。

我们的服务器将使用在节点环境上运行的 Express 服务器。尽管您可能熟悉在持久数据库上运行 Express server，如 [MongoDB](https://www.mongodb.com/) 、 [DynamoDB for AWS](https://n2ws.com/blog/aws-cloud/amazon-dynamodb-latest) 或 [MySQL](https://www.mysql.com/) ，但缓存数据库还是有些不同。对于部署，您可以使用 Amazon ElastiCache for Redis 或使用类似于[Redis Enterprise Cloud for AWS](https://aws.amazon.com/marketplace/pp/B01LZM49M6?qid=1542569179240&sr=0-1&ref_=srh_res_product_title)的工具进行设置。我们不会在本教程中涉及这一点。

## 设置基础知识

你可以在 [GitHub](https://github.com/blizzerand/redis-realtime-chat) 上找到这个聊天应用的源代码。您需要运行 npm 安装，然后启动服务器和前端应用程序。

在开始之前，我们假设您已经安装了 npm 和 node 等基础知识。

如果还没有这样做，您还需要在我们开始之前安装 create-react-app。这可以通过执行以下命令来完成:

```
 npm --global i create-react-app 
```

完成后，您现在可以通过执行以下命令来生成将用于套接字实验的应用程序:

```
create-react-app socket-redis-chat 
```

生成应用程序后，使用您喜欢的文本编辑器打开文件夹。要运行该项目，您需要从应用程序文件夹中运行“npm start”。

在本文中，我们将在同一个代码库中运行客户机和服务器。虽然这可能不会在生产应用程序中完成，但在这里解释起来会更容易。

## 服务器上的 Socket.io 和 Redis

要创建 websocket 服务，只需导航到应用程序文件夹中的终端并安装 [socket.io](https://github.com/socketio/socket.io) :

```
npm i --save socket.io redis socket.io-redis 
```

通过使用 [socket.io-redis](https://github.com/socketio/socket.io-redis) 适配器运行 socket.io，您可以在不同的进程或服务器中运行多个 socket.io 实例，它们都可以相互广播和发送事件。一旦安装了 socket.io，您需要在之前生成的应用程序的根文件夹中创建一个名为“server.js”的文件。

在该文件中，键入以下代码以导入并开始构造套接字:

```
const io = require('socket.io')();
var redis = require('redis');
var redis2 = require('socket.io-redis');

var pub = redis.createClient();
var sub = redis.createClient();
io.adapter(redis2({ host: 'localhost', port: 6379 })); 
```

为了开始构造套接字，我们将使用 io 变量。我们还将初始化一个 [PubSub 架构](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)，并设置 io 适配器来使用`localhost`。

套接字本质上是服务器和客户端之间的一组长双工通道。因此，服务器上的关键部分是成功处理来自客户端的连接。这将允许您将事件发布到相应的客户端。这可以通过下面的代码来完成:

```
io.on('connection', (client) => {
  io.sockets.on('connection', function (socket) {
    socket.removeAllListeners()
    // here you can start emitting events to the client 
  })
}); 
```

`socket.removeAllListereners()`命令用于在启动应用程序时移除任何现有的套接字。现在，您需要通知 socket.io 开始监听客户端:

```
const port = 8000;
io.listen(port);
console.log('listening on port ', port); 
```

现在，您可以导航到您的终端，并通过执行“节点服务器”来启动服务器。一旦启动，您应该会看到以下消息——“监听端口 8000”

在这个阶段，套接字并没有真正接合。虽然您可以访问客户端套接字，但是还没有向它们传输任何东西。但是，由于已向连接的客户端授予了访问权限，因此您将能够响应从客户端传输的事件。您可以将它想象成类似于服务器端的事件处理程序，处理来自特定客户端的特定事件。

第一个目标是让服务器响应来自客户端的 setUsername 请求。该服务应该让用户知道他们在线，并且客户端应该能够向服务器套接字发送数据。您可以修改代码以添加以下内容:

```
socket.on("setUsername", function (data) {

       console.log("Got 'setUsername' from client, " + JSON.stringify(data));
       var reply = JSON.stringify({
               method: 'message',
               sendType: 'sendToSelf',
               data: `${data.user} is now online`
           });    

       socket.join("work");
       pub.publish("work", reply);
   }); 
```

现在，用户能够设置用户名，您可以使用 socket.join 将客户端添加到特定的房间。我们称之为“工作”。pub.publish()将我们创建的 JSON 发布到“工作”频道。

接下来，我们将编写发送消息的逻辑。

```
 socket.on("sendMessage", function (data) {
   console.log("Got 'sendMessage' from client , " + JSON.stringify(data));
       var reply = JSON.stringify({
               method: 'message',
               sendType: 'sendToAllClientsInRoom',
               data: data.user + ":" + data.msg
           });

       pub.publish("work",reply);

   }); 
```

程序非常相似。我们就把它转换成 JSON，发布到频道里。

到目前为止，我们已经讨论了两个案例。用户:
加入频道
发送消息

接下来，我们需要在用户退出时断开他的连接。为此，您可以使用以下代码:

```
 socket.on('disconnect', function () {
       sub.quit();
       pub.publish("chatting","User is disconnected :" + socket.id);
   }); 
```

您的套接字现在应该已经启动并开始寻找客户端。当客户端连接时，您将拥有一个闭包，在这里您将能够处理来自特定客户端的事件。您还应该能够处理从客户端传输的特定事件，如`setUsername`。

那么，我们如何传播这个信息呢？我们将使用 Redis 订户模式来解决这个问题。

```
sub.on("message", function (channel, data) {
 data = JSON.parse(data);
 console.log("Inside Redis_Sub: data from channel " + channel + ": " + (data.sendType));
 if (parseInt("sendToSelf".localeCompare(data.sendType)) === 0) {
      io.emit(data.method, data.data);
 }else if (parseInt("sendToAllConnectedClients".localeCompare(data.sendType)) === 0) {
      io.sockets.emit(data.method, data.data);
 }else if (parseInt("sendToAllClientsInRoom".localeCompare(data.sendType)) === 0) {
     io.sockets.in(channel).emit(data.method, data.data);
 }      

}); 
```

让我们一步一步来。当您调用`pub.publish()`时，订阅者进程接收传递给它的消息。我们创建一个接受两个参数的函数，第一个是通道，第二个称为数据。`data.sendType`保存了我们希望消息被广播的受众的详细信息。这就完成了服务器端的`socket.io`设置。现在是时候转向客户端了。

通过在终端中执行“节点服务器”来确保最新的服务器正在运行。如果终端在最后一次更改时已经处于活动状态，只需重新启动它就足够了。

## 客户端上的 Socket.io

在本指南的前面，我们通过在命令行上执行“npm start”来启动 React 应用程序。因此，现在您应该能够查看、修改您的代码，并看到浏览器用您所做的更改重新加载您的应用程序。

首先，您需要启动将与服务器端套接字通信的客户端套接字代码。为了简单起见，我将把所有的 API 调用放在一个单独的文件中。为此，在`src`文件夹中创建一个文件，并将其命名为`api.js`。在这个文件中，我们将创建套接字函数来完成通信周期。

一个很好的起点是定义函数并将其从模块中导出:

```
 function setUsername(username) {
 /* To be completed */ 
}

function setMessage(username, message) {
 /* To be completed */ 
}

function subscribeToMessages(cb) {
 socket.on('message', function(message){
   /* To be completed */ 

}) ;
}

function disconnectSocket(cb) {
 socket.on('disconnect', function(message) {
  /* To be completed */ 

 })
}

export { disconnectSocket, setMessage, setUsername, subscribeToMessages }; 
```

我们在这里所做的基本上是创建与我们之前创建的套接字函数相对应的函数。

由于我们需要与服务器套接字通信，所以我们需要安装客户端库，`socket.io`。这可以在 NPM–`npm I -- save socket.io-client`的帮助下在命令行上安装。

现在已经安装好了，可以导入了。因为我们在客户端运行代码，所以我们可以利用 ES6 模块的语法。客户端代码将通过 Babel 和 Webpack 传输。

还可以通过从模块`socket.io-client`中调用主要导出功能并分配一个端口来构建套接字。在本例中，端口是 8000:

```
import openSocket from 'socket.io-client';
const socket = openSocket('http://localhost:8000'); 
```

接下来，让我们填写套接字函数的代码。

```
function setUsername(username) {
 console.log(username);
 socket.emit('setUsername',{user:username});
}

function setMessage(username, message) {
 const msg =   {user:username, msg: message};
 socket.emit('sendMessage',msg);
}

function subscribeToMessages(cb) {
 socket.on('message', function(message){
   console.log(message);
   //alert('received msg=' + message);
  cb(null, message)
}) ;
}

function disconnectSocket(cb) {
 socket.on('disconnect', function(message) {
   cb(null, message)
 })
} 
```

需要注意的一点是，`subscribeToMessages`事件需要在套接字上订阅。这需要在传输事件之前完成。我们将在下一步中研究这个问题。

## 使用 React 组件中的事件

您现在应该在客户端有一个`api.js`文件。这将导出可以调用的函数，以便订阅和发出事件。在下一阶段，我们将讨论如何在 React 组件中利用这个函数来接受输入，然后呈现消息。

首先，在使用 create-react-app 生成的`App.js`文件的顶部导入之前创建的 API。

```
import { disconnectSocket, setMessage, setUsername, subscribeToMessages } from './test'; 
```

接下来，让我们定义应用程序的状态:

```
 state = {
    username: '',
    room: '',
    message: '',
    messageList: []
  }; 
```

一旦完成，就该向文件组件添加构造函数了。在这个构造函数中，您可以调用我们从 API 文件中接收的`subscribetoMessages`函数。

每次收到事件时，我们可以使用从服务器收到的值设置一个值，称为“消息”。然后，我们会将该消息附加到现有消息列表中，如下所示:

```
 constructor(props) {
    super(props);

    subscribeToMessages((err, message) => {
      console.log(message);
      this.setState({
      messageList: [...this.state.messageList, message]
    }

  )})
  } 
```

最后，添加处理事件的方法- `setUsername`、`setMessage`和`disconnectSocket`(当组件卸载时)。

```
 componentWillUnmount() {
    disconnectSocket((err, message) => {
      console.log(message)
    });
  }

  submitChat() {

    if(this.state.username   && this.state.message) {
      setMessage(this.state.username, this.state.message);
    }
  }

  submitUsername() {
    if(this.state.username!="") {
      setUsername(this.state.username);
    } 
```

您应该能够在浏览器中查看来自服务器的事件，并在 React 组件中呈现这些事件。

## 总结

唷！我们已经走过了很多地方。虽然这里的代码非常基础，但是它工作得足够好，并且您可以根据自己的需要对其进行扩展，以处理更复杂的问题，而不会遇到很多障碍。当谈到使用 Socket.io 和 React 开发实时应用程序时，我们只是触及了冰山一角。