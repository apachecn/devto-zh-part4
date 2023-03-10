# iOS 和 Android 版 React Native 中的多人井字游戏:游说和加入

> 原文：<https://dev.to/pubnub/multiplayer-tic-tac-toe-game-in-react-native-for-ios-and-android-lobby-and-joining-2mck>

井字游戏是一个经典的纸笔游戏，我们都太熟悉了。井字游戏的基本规则很简单:两个玩家，X 和 O，轮流将他们的棋子放在 3x3 的桌子上的一个正方形中。当两个玩家中的一个将他们的三个棋子放在水平、垂直或对角线的行中时，就宣布获胜者。

在本系列教程中，我们将在 React Native 中开发经典的井字游戏，并允许两个玩家实时对战。在这一部分(第一部分)，我们将建立一个大厅，玩家可以在这里输入他们的用户名，然后创建或加入一个房间来玩。在第二部分中，我们将实现并测试游戏。

## 多人游戏和公共游戏

我们的目标是为玩家提供一个[互联共享的体验](https://www.pubnub.com/blog/connected-shared-experiences-a-developers-guide/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)，在这里他们可以随时随地与世界各地的朋友一起玩。为此，我们将使用 PubNub 来支持我们游戏的实时基础设施，因此我们将专注于为玩家开发出色的体验。

PubNub 提供了一个安全、可扩展和可靠的实时基础设施，通过其全球[数据流网络](https://www.pubnub.com/products/global-data-stream-network/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)为任何应用提供动力。PubNub 拥有超过[70+SDK](https://www.pubnub.com/docs/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)支持大部分编程语言，使得在任何设备上发送和接收消息的时间不超过 100 毫秒。

我们使用 [PubNub React SDK](https://www.pubnub.com/docs/react-native-javascript/pubnub-javascript-sdk/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22) 将两个玩家连接到一个游戏频道，他们将在那里相互对战。玩家的每一步棋都会以 JSON 有效载荷的形式发布到频道上，这样其他玩家的表格就会随着当前的棋步而更新。通过实时更新每一步棋的牌桌，玩家会觉得他们就像是在一起玩一样！

您可以在 [GitHub 资源库](https://github.com/ocastroa/react-native-tictactoe)中查看完整的项目。

## 教程概述

我们的应用程序可以在 Android 和 iOS 上运行(React Native 的优点)。这是我们完成后的样子:

[![Android/iOS screenshot](img/d3b1cd948d9e52dbddfd0b7b444acf07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TKzHUAo9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/android-ios-game.png)

我们增加了一个大厅，玩家可以加入或者创建一个房间。如果一个玩家创建了一个房间，他们成为房间的创建者，并等待另一个玩家加入他们的房间。

[![Create room channel](img/4da7686adfa4510f5ce043cf3d4001f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G6vQVV71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/create-room-channel.gif)

如果另一个玩家想加入同一个房间，他们在警告提示中输入房间名称。该玩家成为对手。

[![Join room channel](img/031bc770e8a9580a5b66387b5f6a9cdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E5H8Y7IF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/join-room-channel.gif)

如果对手试图加入已经有两个人的房间，他们将无法加入。但是，如果房间只有一个玩家，对手将能够加入房间，游戏将为两个玩家开始。游戏开始后，会显示井字游戏棋盘，以及玩家的初始分数。

[![Start of a new game](img/07780d11ac1412f6f86deaed2ec28e4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mhbGCymc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/start-of-game.gif)

如果比赛以平局结束，那么双方都得不到一分。但是如果有赢家，赢家的分数就会更新。房间创建者会收到一个提示，询问他们是想再玩一轮还是退出游戏。 如果房间创建者继续游戏，棋盘将重置为新一轮。如果房间创建者决定退出游戏，两个玩家都将返回大厅。

[![Exit game and go to the lobby](img/698f2b82def96d2250df6710fbeb9a75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BpozQWhF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/exit-to-lobby.gif)

在我们开始实现这个游戏之前，你需要注意一些需求。

## 设置 App

如果你的机器上还没有 React Native 设置，那么按照[的指示开始](https://facebook.github.io/react-native/docs/getting-started.html)。确保您点击第二个选项卡 **React Native CLI Quickstart** ，并选择您的**目标操作系统**。按照文档中的其余说明安装依赖项。

在您的终端中，转到您想要保存项目的目录，并键入以下内容来创建一个新的应用程序:

```
react-native init ReactNativeTicTacToe 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要安装五个依赖项，并将它们链接到您刚刚创建的应用程序。为了简单起见，将下面的脚本文件 **dependencies.sh** 添加到应用程序的根目录:

```
npm install --save pubnub pubnub-react
npm install --save react-native-prompt-android
npm install --save react-native-spinkit
npm install --save shortid
npm install --save lodash
react-native link 
```

Enter fullscreen mode Exit fullscreen mode

使用命令
使脚本可执行

```
chmod +x dependencies.sh 
```

Enter fullscreen mode Exit fullscreen mode

使用
运行脚本

```
./dependencies.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在你的机器已经设置好了，注册一个[免费的 PubNub 账户](https://dashboard.pubnub.com/signup/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)。你可以在[的 PubNub Admin Dashboard](https://admin.pubnub.com/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22) 中获得你唯一的 pub/sub 密钥。

现在我们已经了解了需求，让我们开始编码吧！

## 初始化项目

在你的应用程序的根目录下创建一个名为 **index.js** 的文件，并复制以下代码:

```
import {AppRegistry} from 'react-native';
import App from './App.js';
import {name as appName} from './app.json';

AppRegistry.registerComponent(appName, () => App); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建一个名为 **App.js** 的新文件。这是游戏的主文件，它包含大厅和桌子的组件。为了使 **App.js** 易于理解，我们将把它分成几个独立的部分，并对每个部分进行详细说明。让我们首先导入将在整个项目中使用的组件和依赖项。

```
import React, { Component } from 'react';
import PubNubReact from 'pubnub-react';
import {
  Platform,
  StyleSheet,
  View,
  Alert,
  Text,
} from 'react-native';

import Game from './src/components/Game';
import Lobby from './src/components/Lobby';
import shortid  from 'shortid';
import Spinner from 'react-native-spinkit';
import prompt from 'react-native-prompt-android'; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，添加基本构造函数，您将在其中插入 Pub/Sub 键以连接到 PubNub，初始化本地状态对象并初始化变量。

```
export default class App extends Component {
  constructor(props) {
    super(props);
    this.pubnub = new PubNubReact({
      publishKey: "ENTER_YOUR_PUBLISH_KEY_HERE",
      subscribeKey: "ENTER_YOUR_SUBSCRIBE_KEY_HERE"
    });

    this.state = {
      username: '',
      piece: '', // Creator of the room is 'X' and the opponent is 'O'
      x_username: '', // Username for the room creator
      o_username: '', // Username for the opponent
      is_playing: false, // True when the opponent joins a room channel
      is_waiting: false, // True when the room creator waits for an opponent
      is_room_creator: false, 
      isDisabled: false // True when the 'Create' button is pressed
    };

    this.channel = null;
    this.pubnub.init(this); // Initialize PubNub
  }
 } 
```

Enter fullscreen mode Exit fullscreen mode

稍后，我们将更详细地讨论每个状态对象和变量。另外，确保在初始化状态后初始化 PubNub **。**

接下来，在组件挂载时订阅频道“*game lobby”*。

```
componentDidMount() {
   this.pubnub.subscribe({
     channels: ['gameLobby']
   });
 } 
```

Enter fullscreen mode Exit fullscreen mode

渠道" *gameLobby"* 是玩家在大厅时订阅和发布到的主要渠道。稍后我们将为这个方法添加更多的逻辑。现在，让我们来看看渲染方法。

```
render() {
  return (
    <View style={styles.container}>
      <View style={styles.title_container}>
        <Text style={styles.title}>RN Tic-Tac-Toe</Text>
      </View> 
      <Spinner 
        style={styles.spinner} 
        isVisible={this.state.is_waiting} 
        size={75} 
        type={"Circle"} 
        color={'rgb(208,33,41)'}
      /> 
      {
        !this.state.is_playing &&
        <Lobby 
          username={this.state.name} 
          onChangeUsername={this.onChangeUsername}
          onPressCreateRoom={this.onPressCreateRoom} 
          onPressJoinRoom={this.onPressJoinRoom}
          isDisabled={this.state.isDisabled}
        />
      }

      {
          this.state.is_playing &&
          <Game 
            pubnub={this.pubnub}
            channel={this.channel} 
            username={this.state.username} 
            piece={this.state.piece}
            x_username={this.state.x_username}
            o_username={this.state.o_username}
            is_room_creator={this.state.is_room_creator}
            endGame={this.endGame}
          />
        }
    </View>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

首先显示**大厅**组件，因为 *this.state.is_playing* 被初始化为 false。一旦对手加入了正在等待另一个玩家的房间通道，那么 *this.state.is_playing* 被设置为真，并且**大厅**组件将被**游戏**组件取代。只要房间创建者在等待另一个玩家加入游戏，就会向房间创建者显示**旋转器**组件。

[![Spinner component](img/9c763ea2d82b9d755a01e29835eb7f44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uoOElbhN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/spinner-compnent.gif)

确保在末尾添加样式:

```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: 'white',
  },
  spinner: {
    flex: 1,
    alignSelf: 'center',
    marginTop: 20,
    marginBottom: 50
  },
  title_container: {
    flex: 1,
    marginTop: 18
  },
  title: {
    alignSelf: 'center',
    fontWeight: 'bold',
    fontSize: 30,
    color: 'rgb(208,33,41)'
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们完成其余的 **App.js** 之前，让我们看一下*大厅*组件。

## 实现大堂组件

在大厅里，玩家可以输入他们的用户名，创建或加入一个房间。我们在 **App.js** 中实现逻辑，比如保存用户名，当按钮被按下时调用正确的方法。lobby 组件中的所有方法都用来设计按钮的样式，所以我们不会详细讨论这些方法。我们将详细介绍的唯一三个方法是 **onChangeUsername()** 、 **onPressCreateRoom()** 和 **onPressJoinRoom()** ，它们作为*道具*从 **App.js** 传入。

在应用程序的根目录中，创建一个名为 **src** 的新文件夹，并在该文件夹中创建另一个名为 **components** 的文件夹。在**组件**内部，创建一个名为 **Lobby.js** 的新文件。将以下内容添加到新文件中:

```
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  TextInput,
  TouchableHighlight
} from 'react-native';

export default class Lobby extends Component {
  constructor() {
    super();
    this.state = {
      pressCreateConfirm: false, // Set to true when the Create button is pressed
      pressJoinConfirm: false // Set to true when the Join button is pressed
     };
  }

  onHideUnderlayCreateButton = () => {
    this.setState({ pressCreateConfirm: false });
  }

  onShowUnderlayCreateButton = () => {
    this.setState({ pressCreateConfirm: true });
  }

  onHideUnderlayJoinButton = () => {
    this.setState({ pressJoinConfirm: false });
  }

  onShowUnderlayJoinButton = () => {
    this.setState({ pressJoinConfirm: true });
  }

  render() {
    return (        
      <View style={styles.content_container}>
        <View style={styles.input_container}>
          <TextInput
            style={styles.text_input}
            onChangeText={this.props.onChangeUsername}
            placeholder={" Enter your username"}
            maxLength={15}
            value={this.props.username}
          />
        </View> 
        <View style={styles.container}>
          <View style={styles.buttonContainer}>
            <TouchableHighlight
              activeOpacity={1}
              underlayColor={'white'}
              style={
                this.state.pressCreateConfirm
                    ? styles.buttonPressed
                    : styles.buttonNotPressed
              }
                onHideUnderlay={this.onHideUnderlayCreateButton}
                onShowUnderlay={this.onShowUnderlayCreateButton}
                disabled={this.props.isDisabled}
                onPress={this.props.onPressCreateRoom}
              >
                <Text
                  style={
                  this.state.pressCreateConfirm
                      ? styles.cancelPressed
                      : styles.cancelNotPressed
                      }
                  >
                  Create
                </Text>
            </TouchableHighlight>
          </View> 
          <View style={styles.buttonBorder}/>
            <View style={styles.buttonContainer}>
                <TouchableHighlight
                activeOpacity={1}
                underlayColor={'white'}
                style={
                  this.state.pressJoinConfirm
                      ? styles.buttonPressed
                      : styles.buttonNotPressed
                }
                  onHideUnderlay={this.onHideUnderlayJoinButton}
                  onShowUnderlay={this.onShowUnderlayJoinButton}
                  onPress={this.props.onPressJoinRoom}
                >
                  <Text
                    style={
                    this.state.pressJoinConfirm
                        ? styles.cancelPressed
                        : styles.cancelNotPressed
                        }
                    >
                    Join
                  </Text>
            </TouchableHighlight>
          </View>
        </View>
      </View>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来很多，但实际上，我们在这里所做的只是设置用户名字段和两个按钮。如前所述，我们做的唯一逻辑是设计按钮的样式。在这种情况下，当按下和取消按下按钮时，按钮的背景颜色、边框颜色和文本颜色会发生变化。

[![Button styling demo](img/b50532f3132b798c37cf5f6aa182b43c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g4G9hc8A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/button-styling.gif)

确保将样式添加到文件的末尾:

```
const styles = StyleSheet.create({
  content_container: {
    flex: 1,
  },
  input_container: {
    marginBottom: 20,
  },
  container: {
    flexDirection: 'row',
    paddingLeft: 11,
    paddingRight: 11
  },
  buttonContainer: {
    flex: 1,
    textAlign: 'center',
  },
  buttonBorder: {
    borderLeftWidth: 4,
    borderLeftColor: 'white'
  },
  text_input: {
    backgroundColor: '#FFF',
    height: 40,
    borderColor: '#CCC', 
    borderWidth: 1
  },
  buttonPressed:{
    borderColor: 'rgb(208,33,41)',
    borderWidth: 1,
    padding: 10,
    borderRadius: 5
  },
  buttonNotPressed: {
    backgroundColor: 'rgb(208,33,41)',
    borderColor: 'rgb(208,33,41)',
    borderWidth: 1,
    padding: 10,
    borderRadius: 5
  },
  cancelPressed:{
    color: 'rgb(208,33,41)',
    fontSize: 16,
    textAlign: 'center',
    alignItems: 'center',
  },
  cancelNotPressed: {
    color: 'white',
    fontSize: 16,
    textAlign: 'center',
    alignItems: 'center',
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 保存用户名

每当玩家在用户名字段中键入时，就会调用 **onChangeUsername()** 。这个方法，连同这篇文章中的其他方法，可以在 **App.js** 中找到。

```
onChangeUsername = (username) => {
    this.setState({username});
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将用户名保存在*用户名*状态，并将字符数限制为 **15 个字符**，因此用户名不会太长。如果您愿意，可以增加或减少这个数字。

### 创建房间通道

接下来，让我们实现 **onPressCreateRoom()** 的方法，该方法在用户按下 *Create* 按钮时被调用。

```
onPressCreateRoom = () => {
  if(this.state.username === ''){
    Alert.alert('Error','Please enter a username');
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们首先检查用户名字段是否为空；如果是这样，我们提醒玩家输入用户名。生成一个**随机房间 ID** ，并将其截短为 5 个字符。然后将 **ID** 追加到*‘tictactoe-’*，作为玩家订阅和发布的游戏频道。在 *if* 语句下面，添加以下代码:

```
else{
      let roomId = shortid.generate(); // Random channel name generated
      let shorterRoomId = roomId.substring(0,5); // Truncate to a shorter string value
      roomId = shorterRoomId;
      this.channel = 'tictactoe--' + roomId;
      this.pubnub.subscribe({
        channels: [this.channel],
        withPresence: true
      });
   ... 
```

Enter fullscreen mode Exit fullscreen mode

为了获得频道中的人数，我们使用 [Presence](https://www.pubnub.com/docs/react-native-javascript/presence/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22) 。我们关心频道占用率，因为我们只希望两个人同时连接到一个频道，因为井字游戏是两个人玩的游戏。这就是为什么我们将*和*设置为真。

一旦房间创建者订阅了新频道，我们会提醒他们与朋友分享**房间 ID** 。

[![Share the room id](img/447ad949a2aae71547cc9b05ab6f93e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RbeL0Iqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/share-roomid.png)

将下面的代码添加到我们上面使用的 *else* 语句中:

```
// alert the room creator to share the room ID with their friend
Alert.alert(
  'Share this room ID with your friend',
  roomId,
  [
    {text: 'Done'},
  ],
  { cancelable: false }
); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们想要改变某些对象的状态，所以我们使用 **setState()** 来这样做。在警报下方，仍然在 *else* 语句中，添加以下内容:

```
this.setState({
      piece: 'X',
      is_room_creator: true,
      is_waiting: true,
      isDisabled: true
    });

    this.pubnub.publish({
      message: {
        is_room_creator: true,
        username: this.state.username
      },
      channel: 'gameLobby'
    });  
  } // Close the else statement 
```

Enter fullscreen mode Exit fullscreen mode

改变四个对象的状态后，布尔*为 _ 房间 _ 创建者*，房间创建者的*用户名*将被发布到*游戏大厅*。当房间创建者等待有人加入游戏时，旋转组件将显示给他们。

回到*componentidmount()*，我们需要设置一个监听器来监听到达“*游戏大厅”*的某些消息。

```
componentDidMount(){
  ...
  this.pubnub.getMessage('gameLobby', (msg) => {
    // Set username for Player X
    if(msg.message.is_room_creator){
      this.setState({
        x_username: msg.message.username
      })
    }
   ...
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想获得房间创建者的用户名，所以我们做了一个 *if* 语句来检查到达的消息是否是*msg . message . is _ room _ creator*。如果是这样，我们将状态*x _ 用户名*更改为房间创建者用户名。

### 加入房间通道

我们现在将实现最后一个方法，***【onPressJoinRoom()】**。*

```
onPressJoinRoom = () => {
  if(this.state.username === ''){
    Alert.alert('Error','Please enter a username');
  }
  else{
    // Check for platform
    if (Platform.OS === "android") {
      prompt(
        'Enter the room name',
        '',
        [
         {text: 'Cancel', onPress: () => console.log('Cancel Pressed'), style: 'cancel'},
         {text: 'OK', onPress: (value) =>  
         (value === '') ? '' : this.joinRoom(value)},
        ],
        {
            type: 'default',
            cancelable: false,
            defaultValue: '',
            placeholder: ''
          }
      );      
    }
    else{
      Alert.prompt(
        'Enter the room name',
        '',
        [
         {text: 'Cancel', onPress: () => console.log('Cancel Pressed'), style: 'cancel'},
         {text: 'OK', onPress: (value) =>  
         (value === '') ? '' : this.joinRoom(value)},
        ],
        'plain-text',
      );
    }  
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们确保用户名字段不为空。如果它不是空的，那么提示对手输入房间名。

[![Join room alert prompt](img/26dbfb47879844d6f4e269cf6b8c55ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mr3ilnnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/join-room-alert.png)

由于我们希望考虑两个平台(iOS 和 Android)，我们检查应用程序运行在哪个平台上，并使用适当的提示。对于 Android，我们使用提示依赖**react-native-prompt-Android**。我们这样做是因为 **Alert.prompt()** 只支持 iOS 设备。本质上，两个提示完成了相同的目标:当按下 **OK** 时，调用 **joinRoom(value)** ，其中**值**是房间名称，不能为空值。

```
joinRoom = (room_id) => {
  this.channel = 'tictactoe--' + room_id;

  // Check that the lobby is not full
  this.pubnub.hereNow({
    channels: [this.channel], 
  }).then((response) => { 
    // If totalOccupancy is less than or equal to 1, then the player can't join a room since it has not been created
    if(response.totalOccupancy <= 1){
      Alert.alert('Lobby is empty','Please create a room or wait for someone to create a room to join.');
    }
    // Room is available to join
    else if(response.totalOccupancy === 2){
      this.pubnub.subscribe({
        channels: [this.channel],
        withPresence: true
      });

      this.setState({
        piece: 'O',
      });  

      this.pubnub.publish({
        message: {
          readyToPlay: true, // Game can now start
          not_room_creator: true,
          username: this.state.username
        },
        channel: 'gameLobby'
      });
    } 
    // Room already has two players
    else{
      Alert.alert('Room full','Please enter another room name');
    }
  }).catch((error) => { 
      console.log(error)
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不希望两个以上的人在同一个游戏通道中，所以我们使用 [hereNow()](https://www.pubnub.com/docs/react-native-javascript/api-reference-presence#here_now/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22) 函数来检查通道的总占用率。如果总占用率小于 1，则玩家试图加入一个尚未创建的房间，或者房间名称有错别字。

如果总占用率为 2，那么通道中有一个玩家，房间创建者，正在等待另一个玩家开始游戏。

如果总占用率大于 2，则玩家正试图加入正在进行游戏的房间，因此警告告诉玩家该房间已满并加入另一个房间。

一旦对手成功订阅了游戏频道，就会发布一条消息，将对手的*用户名*和*准备玩*设置为真。由于玩家不是房间创建者， *not_room_creator* 被设置为真。

### 整理大堂组件

我们将在*componentidmount()*中添加监听器的最后一个逻辑。

```
componentDidMount() {
  ...
  this.pubnub.getMessage('gameLobby', (msg) => {
    ...

    else if(msg.message.not_room_creator){
      this.pubnub.unsubscribe({
        channels : ['gameLobby']
      }); 
      // Start the game
      this.setState({
        o_username: msg.message.username,
        is_waiting: false,
        is_playing: true,
      });  
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

两位玩家都将取消对“*game lobby”*的订阅，因为他们订阅了游戏室频道。在 *setState()* 中，我们做了三件事:将对手用户名设置为 *o_username，*将 *is_waiting* 设置为 false，这样 Spinner 组件将从房间创建者的视图中消失，并将 *is_playing* 设置为 true，这样两个玩家之间的游戏就可以开始了。

我们需要包含的最后一个方法是 **componentWillUnmount()** 。

```
componentWillUnmount() {
  this.pubnub.unsubscribe({
    channels : ['gameLobby', this.channel]
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

当组件被卸载和销毁时，调用此方法。我们取消订阅“ *gameLobby”，*如果用户仍然订阅该频道，以及 *this.channel* ，玩家为游戏订阅的频道。

现在我们已经完成了大厅的逻辑，我们将着手设置游戏桌，添加游戏逻辑，并实现玩家之间的实时交互。进入第二部分！