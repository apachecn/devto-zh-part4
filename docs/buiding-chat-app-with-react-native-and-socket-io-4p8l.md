# 用 React Native 和 Socket.io 构建聊天应用

> 原文：<https://dev.to/kris/buiding-chat-app-with-react-native-and-socket-io-4p8l>

在这篇文章中，我们将学习如何用 React Native 和 Socket.io 构建一个 [React Native 聊天应用](https://www.instamobile.io/app-templates/react-native-chat-app-template/)。socket . io 是一个广泛使用的 JavaScript 库，主要用于实时 web 应用。它支持客户端(浏览器)和服务器之间的实时、双向和基于事件的通信。它由 Node.js 和 JavaScript 客户端库构建而成。其最突出的特点是可靠性，自动重新连接，多组房间支持和检测连接中断。

因此，这些是我们从 socket.io 获得的特性，我们将把它们集成到我们的 React 本地应用程序中，以创建一个聊天应用程序。

所以，让我们开始吧，伙计们！！！

### Nodejs backend

首先，我们需要一个后端服务器来处理所有来自客户端的消息和请求。

这里，我们需要在我们的节点项目中包含 express 和 socket.io 到 index.js 文件。我们将使用 es5 方法导入所需的 express 和 socket.io 库。但是你也可以使用 es6 编码格式来导入。我们将把 express 方法初始化为一个常量变量 app，然后用 app 变量创建 http 服务器。之后我们要用 socket.io 库监听服务器。服务器将要监听的端口设置为 3000。

```
const express = require("express");
const app = express();
const server = require("http").createServer(app);
const io = require("socket.io").listen(server);
const port = 3000; 
```

其次，我们需要用 on 方法启动 socket.io 服务器。然后，我们需要发出函数来建立双向通信，如下面的代码片段所示:

```
io.on("connection", socket => {
  console.log("a user connected :D");
  socket.on("chat message", msg => {
    console.log(msg);
    io.emit("chat message", msg);
  });
}); 
```

最后，我们将在端口 3000 上启动服务器，以便服务器可以在该端口监听任何传入的请求。

```
server.listen(port, () => console.log("server running on port:" + port)); 
```

您可以在 [Codesandbox](https://codesandbox.io/s/trusting-allen-g0ztf) 中查看服务器端代码和演示的结果。这就完成了我们的服务器设置。

### 反应原生节

在这一步中，我们将假设我们已经设置了 React 本地项目，并且它正在运行。现在，我们继续构建应用程序，并将服务器端集成到我们的客户端聊天应用程序中。

这里，首先我们需要导入 socket.io 客户端包，即从 socket.io-client 包中导入 io 模块，如下面的代码片段所示:

```
import io from "socket.io-client"; 
```

接下来，我们需要创建两种状态来处理书面消息和保存消息的数组中的变化。这里的两个状态是处理客户端输入的 chatMessage 和处理提交或接收消息的存储的 chatMessages 数组。下面的代码片段给出了这方面的代码:

```
constructor(props) {
   super(props);
   this.state = {
      chatMessage: "",
      chatMessages: []
   };
} 
```

现在，我们初始化与服务器的连接。

这里，通过使用 io 模块的 on 方法，我们将通过连接发送或接收的新消息来更改*数组*的状态，如下面的代码片段所示:

```
componentDidMount() {
   this.socket = io("http://127.0.0.1:3000");
    this.socket.on("chat message", msg => {
          this.setState({ chatMessages: [...this.state.chatMessages, msg]   
     });
  });
} 
```

接下来，我们需要创建一个简单的表单，并用 map 函数显示消息。我们将使用简单的 TextInput 元素让客户端输入消息，并在 onSubmitEditing 事件发生时触发 submitChatMessage 函数，如下面的代码片段所示:

```
render() {
    const chatMessages = this.state.chatMessages.map(chatMessage => (
      <Text style={{borderWidth: 2, top: 500}}>{chatMessage}</Text>
    ));

    return (
      <View style={styles.container}>
        {chatMessages}
        <TextInput
          style={{height: 40, borderWidth: 2, top: 600}}
          autoCorrect={false}
          value={this.state.chatMessage}
          onSubmitEditing={() => this.submitChatMessage()}
          onChangeText={chatMessage => {
            this.setState({chatMessage});
          }}
        />
      </View>
  ` );
  } 
```

创建 submitChatMessage 函数是为了使用 emit 方法将消息发送到服务器，如下面的代码片段所示:

```
submitChatMessage() {
    this.socket.emit('chat message', this.state.chatMessage);
    this.setState({chatMessage: ''});
  } 
```

最后，我们将使用样式表组件向聊天容器添加一些样式，如下面的代码片段所示:

```
const styles = StyleSheet.create({
  container: {
    height: 400,
    flex: 1,
    backgroundColor: '#F5FCFF',
  },
}); 
```

现在，我们在设备仿真器或测试上运行我们的项目。正如我们所见，它如预期的那样工作:

[![](img/ce108856198843f76819002ae0247b09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bq2bb0_w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/810/1%2AkZkmBqwGDVZ4L7ueEXSkkw.gif)

因此，这标志着我们使用 React Native 和 Socket.io 包的简单聊天应用程序的成功完成。

### 结论

在这篇文章中，我们学习了如何使用 Node 和 express 创建和设置一个简单的 socket.io 服务器。然后，我们还学习了如何将客户端 React 本地聊天应用程序与服务器端集成。我们成功地建立了双向沟通。最后，我们能够在设备模拟器中成功地运行和测试我们的聊天应用程序。

*原载于 2019 年 8 月 19 日*[*https://kriss . io*](https://kriss.io/buiding-chat-app-with-react-native-and-socket-io/)*。*

### 交底

这篇文章包括附属链接；如果您从本文提供的不同链接购买产品或服务，我可能会收到报酬。

* * *