# 构建 React 本地聊天应用–第一部分:基本消息

> 原文：<https://dev.to/kris/building-a-react-native-chat-app-part-one-basic-messaging-39dl>

### 获得全反应原生信使克隆体

想为 Android 和 iOS 开发一款有趣的消息应用吗？使用我们外观精美、功能齐全的[社交聊天应用模板](https://www.instamobile.io/app-templates/react-native-chat-app-template/)启动 react 原生聊天应用。它用 React Native 编写，具有强大的现成功能，如群聊、一对一聊天、照片消息和友谊管理。

欢迎来到我们关于使用 React Native 和 PubNub 构建移动聊天应用的成长系列的第一部分。PubNub 提供了我们的[实时消息聊天 API](https://www.pubnub.com/solutions/chat/)和后端， [React Native Gifted Chat](https://github.com/FaridSafi/react-native-gifted-chat) 为我们的应用程序提供了完整的聊天 UI。

在这种情况下，我们将使用 [PubNub React 包](https://www.npmjs.com/package/pubnub-react)，它是 PubNub JavaScript SDK (v4)的包装器。它简化了 PubNub 与 React 或 React Native 的集成。

最终，我们将有一个简单的移动群聊应用程序，允许多个用户实时相互发送消息。从那里，我们将继续添加新的聊天功能，包括[消息历史](https://www.pubnub.com/blog/building-a-chat-app-with-react-native-and-pubnub-part-two-message-history/)，用户在线/离线状态，以及输入指示器。

## 要求

开始之前，请确保您具备以下条件:

*   [Node.js 安装](https://nodejs.org/en/download/)，还有 NPM 或者纱。
*   一个 [PubNub 账户](https://dashboard.pubnub.com/signup)；这是您获得唯一发布/订阅密钥的地方。
*   两台(或更多)移动设备来测试应用程序。

## 设置

在这一步，我们将使用下面的命令创建一个 React 本地项目:

```
react-native init pubnubchatwithreactnative 
```

用 VSCode 或您喜欢的代码编辑器打开名为“pubnubchatwithreactnative”的项目文件夹。接下来，打开一个集成的终端，使用下面的代码片段运行 Android 和 iOS 模拟器:

```
react-native run-ios

react-native run-android 
```

注意:React 项目的第一次构建将花费相当多的时间。构建完成后，您将在 Android 和 iOS 模拟器上看到以下内容。

[![React Native Simulator Default Screens iOS Android](img/0290ba17f5db9350163c362b16812dfa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N2Pn0eeV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/android-and-ios-simulators-react-native-screenshot.png)

## UI:安装赠品聊天 UI 包

在这一步，我们将安装天赋聊天包，为我们提供一个完整的聊天 UI。您可以使用以下命令安装 [react-native-gifted-chat](https://github.com/FaridSafi/react-native-gifted-chat) :

[使用 NPM](https://www.npmjs.com/#getting-started) :

```
npm install react-native-gifted-chat --save 
```

[使用纱线](https://yarnpkg.com/en/) :

```
yarn add react-native-gifted-chat 
```

从 GitHub *readme* 文件中抓取示例代码并粘贴到 *App.js* 文件中。

接下来，从 react-native-gifted-chat 包中导入 *GiftedChat* 组件/模块，如下面的代码片段所示:

```
import { GiftedChat } from 'react-native-gifted-chat' 
```

用下面这段代码替换默认类中的代码:

```
export default class App extends Component {
  state = {
    messages: [],
  };
  componentWillMount() {
    this.setState({
      messages: [
        {
          _id: 1,
          text: "Hello developer",
          createdAt: new Date(),
          user: {
            _id: 2,
            name: "React Native",
            avatar: "https://placeimg.com/140/140/any",
          },
        },
      ],
    });
  }
  onSend(messages = []) {
    this.setState(previousState => ({
      messages: GiftedChat.append(previousState.messages, messages),
    }));
  }
  render() {
    return (
      <GiftedChat
        messages={this.state.messages}
        onSend={messages => this.onSend(messages)}
        user={{
          _id: 1,
        }}
      />
    );
  }
} 
```

如上面的代码片段所示， *onSend()* 函数用于收集消息并与之前的消息连接。你会在聊天界面看到。导入组件 *GiftedChat* 用于覆盖整个聊天界面。

您需要保存代码并刷新模拟器。您将看到一条示例消息。

[![Example chat message in the iOS and Android emulators withReact Native](img/1e43db949454fa39a1ccc697ee108c07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aN73Qk3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/react-native-chat-simulator-screenshot.png)

请记住，您将无法发送消息，因为 PubNub 尚未实现。

## 消息传递和后端:实现 PubNub

现在我们已经准备好了我们的 UI，是时候让我们的聊天应用程序具有交互性了——允许用户实时交流。这就是 PubNub 的用武之地。

在这一步，我们将安装 PubNub SDK。您可以使用以下命令安装该软件包。

使用 NPM:

```
npm install pubnub-react --save 
```

使用纱线:

```
yarn add pubnub-react 
```

一旦安装完成，你需要从 *pubnub-react* 包中导入 *PubNubReact* 模块，如代码所示:

```
import PubNubReact from 'pubnub-react'; 
```

接下来，你可以从 PubNub React GitHub 的 [README 文件中复制示例代码，粘贴到你的项目
的 *App.js* 文件中](https://github.com/pubnub/react#hello-world-example)

```
constructor(props) {
    super(props);
    this.pubnub = new PubNubReact({ publishKey: 'YOUR PUBLISH KEY', subscribeKey: 'YOUR SUBSCRIBE KEY' });
    this.pubnub.init(this);
  }

  componentWillMount() {
    this.pubnub.subscribe({ channels: ['channel1'], withPresence: true });

    this.pubnub.getMessage('channel1', (msg) => {
      console.log(msg);
    });

    this.pubnub.getStatus((st) => {
      console.log(st);
      this.pubnub.publish({ message: 'hello world from react', channel: 'channel1' });
    });
  }

  componentWillUnmount() {
    this.pubnub.unsubscribe({ channels: ['channel1'] });
  } 
```

接下来，添加您注册 PubNub 时获得的唯一 pub/sub 密钥。它们可以在您的 [PubNub 管理仪表板中找到。](https://dashboard.pubnub.com/)

[![Free PubNub API Keys](img/f1c2da0d159ec13e9ffa6fcba7ca2aa1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XggwG_5Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/pubnub-api-keys-admin-dashboard.png)

保存并重新运行模拟器。要查看 JavaScript 控制台输出，我们需要远程使用 Debug JS。

[![React Native Debugger Screenshot iOS Simulator](img/bcda36973801c46f0b9401c0dc198a29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2CCxts93--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/react-native-debugger.png)

您现在可以在浏览器控制台中看到 PubNub 数据。

[![JS Chrome Debugger with PubNub Messages](img/788d555b9194c9d8168d0cda8877e774.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8xcz6O1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/js-debugger-chrome-react-native-pubnub-messages.png)

至此，PubNub 实现完成。

接下来，我们需要使用 PubNub 配置聊天频道，将其与天才聊天一起设置。遵循以下简单步骤:

*   初始化 PubNub(完成)
*   使用 SDK 订阅方法订阅频道名称“channel1”。
*   用 **publish** 方法向“channel1”发送消息。
*   使用 **getMessagemethod** 从通道获取消息。

## 把所有的东西放在一起

在这一步中，我们将整个配置放在一起。我们将来自天才聊天的消息传递给 PubNub publish()方法。您可以使用下面代码片段中的示例代码。

```
onSend(messages = []) {
    this.pubnub.publish({
      message: messages,
      channel: "Channel1",
    });
  } 
```

当回调函数 *onSend()* 被调用时，我们将向*通道 1* 发送消息。

为了从通道中实时获取消息，我们使用下面的回调来附加先前的状态消息。下面是代码:

```
this.pubnub.getMessage("ReactChat", m => {
  this.setState(previousState => ({
    messages: GiftedChat.append(previousState.messages, m["message"]),
  }));
}); 
```

全部完成！让我们通过重新加载模拟器来尝试一下。现在，您应该在模拟器中看到以下内容。

[![React Native simulators with Gifted Chat powered by PubNub](img/9d6a15f619b6dd2eb7490875701e86a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RisXOUAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/pubnub-gifted-chat-react-native-screenshot.png)

您会看到聊天消息出现在 UI 中，但所有消息都出现在同一侧。我们需要用图形将发送的消息和接收的消息分开，让两个人看起来像是在聊天。

```
<GiftedChat
        messages={this.state.messages}
        onSend={messages => this.onSend(messages)}
        user={{
          _id: 0,
        }}
      /> 
```

这是因为当我们打开一个新的会话时，我们还没有设置一个新的 *id* 。我们需要对*用户 id* 进行硬编码。

在 *App.js* 中创建一个函数，用于生成一个随机的*用户 id* ，如下面的代码片段所示。

```
 randomid = () => {
    return Math.floor(Math.random() * 100);
  }; 
```

设置变量并将其添加到 *App.js* 中的构造函数中。

```
this.id = this.randomid(); 
```

最后，将状态和函数添加到 GiftedChat HTML 中，如下面的代码所示。

```
<GiftedChat
        messages={this.state.messages}
        onSend={messages => this.onSend(messages)}
        user={{
          _id: this.id,
        }}
      /> 
```

现在，重新加载模拟器并观察结果。你会看到聊天信息出现在另一边。

[![React Native PubNub Chat App](img/f753d9b56bbd515473e644cf1b7a1bdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C9wWT5d2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/06/React-Native-PubNub-Chat.gif)

终于来了！我们已经使用 React Native 以及天才聊天和 PubNub 完成了聊天应用程序的设置。

## 包装完毕

在本教程中，我们学习了如何使用天赋聊天和 PubNub 创建一个简单的 React Native 聊天应用程序。您现在已经有了一个基本的移动聊天应用程序，允许用户实时发送和接收消息。

下一步是开始添加额外的聊天功能——[消息历史记录](https://www.pubnub.com/blog/building-a-chat-app-with-react-native-and-pubnub-part-two-message-history/)、输入指示器、用户在线状态等。请关注后续的帖子！

本教程的所有代码都可以在我的 GitHub repo 中找到[。](https://github.com/krissnawat/pubnub-react-native-chat/tree/637678276c5033086f0c0323c54f423c41c62c57)

帖子[构建一个 React 本地聊天应用——第一部分:基本消息](https://www.pubnub.com/blog/building-a-chat-app-with-react-native-and-pubnub-part-one-messaging/)最先出现在[的 PubNub](https://www.pubnub.com) 上。

## 此处继续

[![kris image](img/6a14a5301207283abbe9d35f765c09de.png)](/kris) [## 构建 React 本地聊天应用程序——第二部分:消息历史

### 克里斯 7 月 5 日 197 分钟阅读

#build #mobile #react #reactnative](/kris/building-a-react-native-chat-app-part-two-message-history-1d8k)