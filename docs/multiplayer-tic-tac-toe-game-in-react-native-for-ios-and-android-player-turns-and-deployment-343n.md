# iOS 和 Android 版 React Native 中的多人井字游戏:玩家回合和部署

> 原文：<https://dev.to/pubnub/multiplayer-tic-tac-toe-game-in-react-native-for-ios-and-android-player-turns-and-deployment-343n>

欢迎来到我们关于用 React Native 和 PubNub 构建移动多人井字游戏的教程系列的第二部分。在这一节中，我们将使用两种方法来开发实际的游戏和测试游戏。

如果你还没有，在开始本部分之前，检查并浏览一下[第一部分](https://dev.to/pubnub/multiplayer-tic-tac-toe-game-in-react-native-for-ios-and-android-lobby-and-joining-2mck)，因为我们已经初始化了项目并设置了大厅。

您可以在 [GitHub 资源库](https://github.com/ocastroa/react-native-tictactoe)中查看完整的项目。

## 实现游戏组件

从项目根目录，转到 **src/components** ，新建一个名为 **Game.js** 的文件。所有的游戏逻辑都在这个文件中。将以下代码添加到文件中:

```
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableHighlight,
  Alert
} from 'react-native';

import range from 'lodash';
const _ = range;

export default class Game extends Component {

  constructor(props) {
    super(props);   

    // Winning combinations
    this.possible_combinations = [
      [0, 3, 6],
      [1, 4, 7],
      [0, 1, 2],
      [3, 4, 5],
      [2, 5, 8],
      [6, 7, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];

    // Corresponds to the square number on the table
    this.ids = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8]
    ];

    // For the 3x3 table
    this.rows = [
      _.range(3).fill(''),
      _.range(3).fill(''),
      _.range(3).fill(''),
    ];

    this.state = {
      moves: _.range(9).fill(''),
      x_score: 0,
      o_score: 0,
    }
    this.turn = 'X' // Changes every time a player makes a move
    this.game_over = false; // Set to true when the game is over
    this.count = 0; // used to check if the game ends in a draw
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在**基本构造函数**中做了几件事:

1.  首先，我们设置了一系列可能的组合来赢得游戏。如果一个玩家符合任何组合，该玩家就是游戏的赢家。
2.  其次，我们设置了另一个名为 **ids** 的数组，它用一个惟一的 id 标记表格中的每个方块，以检查方块是否被占用。
3.  然后，我们设置了包含三个空数组的*行*，每个数组的大小为三。
4.  之后，对于状态对象，我们初始化*移动*为一个空字符串值的数组，并将两个玩家的分数初始化为 0。数组*移动*将被用来检查是否有赢家，我们将在后面看到。
5.  最后，我们添加三个变量，*回合，* *游戏结束*和*计数*，它们将在整个游戏中有用。

## 设置用户界面

让我们为组件设置 UI，它包括表格和每个玩家的当前分数和用户名。此外，包括风格。

```
render() {
  return (
    <View style={styles.table_container}>
      <View style={styles.table}>
        {this.generateRows()}
      </View> 
      <View style={styles.scores_container}>
        <View style={styles.score}>
          <Text style={styles.user_score}>{this.state.x_score}</Text>
          <Text style={styles.username}>{this.props.x_username} (X)</Text>
        </View> 
        <View style={styles.score}>
          <Text style={styles.user_score}>{this.state.o_score}</Text>
          <Text style={styles.username}>{this.props.o_username} (O)</Text>
        </View>
      </View>               
    </View>
  );
 }
}

const styles = StyleSheet.create({
  table_container: {
    flex: 9
  },
  table: {
    flex: 7,
    flexDirection: 'column',
    color: 'black'
  },
  row: {
    flex: 1,
    flexDirection: 'row',
    borderBottomWidth: 1,
  },
  block: {
    flex: 1,
    borderRightWidth: 1,
    borderColor: '#000',
    alignItems: 'center',
    justifyContent: 'center'
  },
  block_text: {
    fontSize: 30,
    fontWeight: 'bold',
    color: 'black'
  },
  scores_container: {
    flex: 2,
    flexDirection: 'row',
    alignItems: 'center'
  },
  score: {
    flex: 1,
    alignItems: 'center',
  },
  user_score: {
    fontSize: 25,
    fontWeight: 'bold',
    color: 'black'
  },
  username: {
    fontSize: 20,
    color: 'black'
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，对于用户名，我们使用 *this.props* ，因为我们从 **App.js** 中获取值。作为复习，这里是我们将使用的来自 **App.js** 的值。

```
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
```

Enter fullscreen mode Exit fullscreen mode

为了构建井字游戏表，我们调用方法 **generateRows()** ，该方法调用 **generateBlocks()** 。这些方法负责创建表。

```
generateRows = () => {
  return this.rows.map((row, col_index) => {
    return (
      <View style={styles.row} key={col_index}>
        {this.generateBlocks(row, col_index)}
      </View>
    );
  });
}

generateBlocks = (row, row_index) => {
  return row.map((block, col_index) => {
    let id = this.ids[row_index][col_index];
    return (
      <TouchableHighlight 
        key={col_index} 
        onPress={
          this.onMakeMove.bind(this, row_index, col_index)
        } 
        underlayColor={"#CCC"} 
        style={styles.block}>

        <Text style={styles.block_text}>
          {
            this.state.moves[id]
          }
        </Text> 
      </TouchableHighlight>     );
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用**touchable highlight**让玩家可以触摸桌子上的任何方块。为了将相应的棋子放在方块上，将方块文本设置为 *this.state.moves[id]* 的值，它将包含进行移动的玩家的正确棋子。这将在下面详细解释。

## 添加逻辑

使用 *row_index* 和 *col_index* 调用 **onMakeMove()** 作为方法参数，row_index 是放置该块的行，col_index 是放置该块的列。

```
onMakeMove(row_index, col_index) {
  let moves = this.state.moves;
  let id = this.ids[row_index][col_index];

  // Check that the square is empty
  if(!moves[id] && (this.turn === this.props.piece)){ 
    moves[id] = this.props.piece;

    this.setState({
      moves
    });

    // Change the turn so the next player can make a move
    this.turn = (this.turn === 'X') ? 'O' : 'X';

    // Publish the data to the game channel
    this.props.pubnub.publish({
      message: {
        row: row_index,
        col: col_index,
        piece: this.props.piece,
        is_room_creator: this.props.is_room_creator,
        turn: this.turn
      },
      channel: this.props.channel
    });
    this.updateScores.call(this, moves);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们通过获取*this . ids[row _ index][col _ index]的值来获得被按压方块的整数 id。**if*语句检查玩家触摸的方格是否是空的，以及当前是否轮到该玩家放置他们的棋子。如果这两个条件都不满足，我们就忽略触摸。如果条件满足，我们将棋子添加到数组*移动*中，以 *id* 作为索引。

例如，如果房间创建者在表格的第 0 行第 2 列上移动，那么*this . ids**【0】【2】*返回整数 2。

```
this.ids = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8]
]; 
```

Enter fullscreen mode Exit fullscreen mode

棋子 *X* 加入数组*移动*到第二个索引:[""，""，，"，""，""，""，" "，" "]。稍后在**的 updateScores()** 中，我们将看到*如何移动*来检查赢家。

回到 **onMakeMove()** ，在我们改变了*移动*的状态后，我们更新了*回合*，这样下一个玩家就可以移动了。我们将数据发布到游戏频道，比如移动的棋子和它被放置的位置。另一个玩家的表会用当前数据更新。最后，调用 **updateScores()** 来检查是否有赢家或者游戏是否以平局结束。

在我们到达 **updateScores()** 之前，在**componentidmount()**中实现通道监听器，以监听来自通道的传入消息。

```
componentDidMount() {
  // Listen for messages in the channel
  this.props.pubnub.getMessage(this.props.channel, (msg) => {
    // Add the other player's move to the table
    if(msg.message.turn === this.props.piece){
      let moves = this.state.moves;
      let id = this.ids[msg.message.row][msg.message.col];

      moves[id] = msg.message.piece;

      this.setState({
        moves
      });

      this.turn = msg.message.turn;
      this.updateScores.call(this, moves);
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

两个玩家都会收到这条消息，因为他们都订阅了同一个频道。当收到消息时，我们执行几个与对 **onMakeMove()** 相同的步骤，特别是更新表格并检查是否有赢家。但是，我们不希望移动的玩家重新做这些步骤。因此，我们做了一个 *if* 语句来确保只有对面的玩家执行这些步骤。我们通过检查*回合*(或者是 *X* 或者是 *O* )是否与玩家的棋子匹配来做到这一点。

现在让我们实现方法 **updateScores()** 。

```
updateScores = (moves) => {
 // Iterate the double array possible_combinations to check if there is a winner
 for (let i = 0; i < this.possible_combinations.length; i++) {
    const [a, b, c] = this.possible_combinations[i];
    if (moves[a] && moves[a] === moves[b] && moves[a] === moves[c]) {
      this.determineWinner(moves[a]);   
      break;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了确定是否有赢家，我们必须迭代 *possible_combinations* 来检查是否有任何组合出现在表中。这就是阵列*移动 _ 派上用场的地方。我们使用*【a，b，c】_ 来得到每个*可能组合的数组*。然后检查是否与*移动*中的任何模式相匹配。为了更清楚地说明这一点，我们来看一个例子。

假设房间创建者在桌子上的第 2 行第 2 列走了一个赢棋，id 为 8。该表将如下图所示:

[![Winning move example](img/2994e80a7422f119761b0e4a1781c18b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y7MX6ghK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/winning-move-example.png)

正如你所看到的，根据他们的 id，获胜的棋步在位置 2、5 和 8。数组*移动*现在:["O "、""、" X "、""、" O "、" X "、""、""、" X"]。在 **updateScores()，**中，我们遍历每一个可能的获胜组合。

对于这个例子，获胜的组合是[2，5，8]。所以当*【a，b，c】*的值为*【2，5，8】*时，循环的*中的 *if* 语句由于*【2，5，8】*都具有相同的值 *X* 。调用 **determineWinner()** 来更新获胜玩家的分数，在本例中，获胜玩家是房间创建者。在我们开始那个方法之前，让我们完成其余的 **updateScores()** 。*

如果没有赢家，游戏以平局结束，那么双方都得不到一分。为了检查是否有平局，在上面的*下面添加下面的代码用于*循环。

```
this.count++;
// Check if the game ends in a draw
if(this.count === 9){
  this.game_over = true;
  this.newGame();   
} 
```

Enter fullscreen mode Exit fullscreen mode

每按一次桌面上的方块，就会调用 **updateScores()** 。如果没有发现获胜者，那么*计数*增加 1。如果*计数*等于 9，游戏以平局结束。当玩家在牌桌的最后一格进行最后一步棋时，这种情况就会发生，因此*计数*从 8 增加到 9。然后调用方法 **newGame()** 。

我们一会儿将回到 **newGame()** 上。我们先实现 **determineWinner()** 。

```
determineWinner = (winner) => {
  var pieces = {
    'X': this.state.x_score,
    'O': this.state.o_score
  }

  // Update score for the winner
  if(winner === 'X'){
    pieces['X'] += 1;
    this.setState({
      x_score: pieces['X']
    });
  }
  else{
    pieces['O'] += 1;
    this.setState({
      o_score: pieces['O']
    });
  }
  // End the game once there is a winner
  this.game_over = true;
  this.newGame();   
} 
```

Enter fullscreen mode Exit fullscreen mode

我们检查获胜者是房间创建者还是对手，并且我们将获胜者的当前分数增加 1 分。分数更新后，我们将 *game_over* 设置为 true，并调用方法 **newGame()** 。

## 游戏结束

```
new_game = () => {
  // Show this alert if the player is not the room creator
  if((this.props.is_room_creator === false) && this.game_over){
    Alert.alert('Game Over','Waiting for rematch...');
    this.turn = 'X'; // Set turn to X so opponent can't make a move
  }

  // Show this alert to the room creator
  else if(this.props.is_room_creator && this.game_over){
    Alert.alert(
      "Game Over!", 
      "Do you want to play another round?",
      [
        {
          text: "Nah", 
          onPress: () => {
            this.props.pubnub.publish({
              message: {
                gameOver: true
              },
              channel: this.props.channel
            });             
          },
          style: 'cancel'
        },
        {
          text: 'Yea', 
          onPress: () => {
            this.props.pubnub.publish({
              message: {
                reset: true
              },
              channel: this.props.channel
            });
          }  
        },
      ],
      { cancelable: false } 
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们向房间创建者和对手显示两种不同的警告。房间创建者的警报有一条消息，询问他们是否想再玩一轮或退出游戏。

[![Game over alert for the room creator](img/8107e1904088eb4db3b1a9afa193f292.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SGyy-IPH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/game-over-alert-room-creator.png)

对于对手，我们向他们显示一个警告信息，告诉他们等待新一轮，这将由房间创建者决定。

[![Game over alert for the opponent](img/4540183928d448043dae76abaa35ed4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LLa7dVIW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/game-over-opponent.png)

如果房间创建者决定结束游戏，那么消息 *gameOver* 被发布到频道；否则消息*重启*被发布到通道。我们通过在***componentidmount()***的 *getMessage()* 中添加更多的逻辑来处理这些消息。

```
componentDidMount() {
  // Listen for messages in the channel
  this.props.pubnub.getMessage(this.props.channel, (msg) => {
    ...

    if(msg.message.reset){
      this.setState({
        moves: _.range(9).fill('')
      });
      this.turn = 'X';
      this.game_over = false;
    }

    if(msg.message.gameOver){
      this.props.pubnub.unsubscribe({
        channels : [this.props.channel]
      });
      this.props.endGame();
    }
  }); 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果房间创建者想再玩一轮，那么我们将桌子重置为游戏开始时的状态。我们这样做是通过设置数组*将*移动到它的原始状态。我们将*复位，将*再次变为 *X* ，并将 *game_over* 复位为假。

但是如果房间创建者决定结束游戏，那么两个玩家都取消订阅当前频道，并且调用方法 **endGame()** 。该方法将结束游戏，并将玩家带回大厅，因为*正在玩*被重置为假。方法 *endGame()* 是来自 **App.js** 的道具，所以我们要回到 **App.js** 来实现。

```
// In App.js
endGame = () => {
  // Reset the state values
  this.setState({
    username: '',
    rival_username: '',
    is_playing: false,
    is_waiting: false,
    isDisabled: false
  });

  // Subscribe to gameLobby again on a new game
  this.channel = null;
  this.pubnub.subscribe({
    channels: ['gameLobby'],
    withPresence: true
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将所有状态值重置回其原始状态，并将*通道*的值重置为空，因为当再次按下*创建*按钮时，将会有一个新的*房间 id* 。最后，再次订阅“*game lobby”*频道，这样玩家就可以继续玩井字游戏。

这就是我们的反应本地井字游戏工作所需的一切！现在，是时候测试这个应用程序了。

## 测试 App

在运行应用程序之前，我们需要启用[在线功能](https://www.pubnub.com/docs/react-native-javascript/api-reference-presence/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)来检测频道中的人数。要打开它，进入[的 PubNub 管理仪表板](https://admin.pubnub.com/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)，点击你的应用程序。点击**按键**并向下滚动至**应用附加模块**。将**存在**开关切换至上的**。保持默认值不变。**

[![Enable presence](img/909467b52979bfb9dd413363a2fc70d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7DxJJmSn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/enable-presence.png)

要运行该应用程序，请确保您在项目文件夹中，并在终端中输入以下命令:

```
react-native run-ios 
```

Enter fullscreen mode Exit fullscreen mode

该命令将打开模拟器并运行应用程序。你也可以通过用 *run-android* 替换 *run-ios* 来在 Android 模拟器上运行应用程序(**注**:确保在运行命令之前先打开一个模拟器)。模拟器打开后，将显示大厅界面。

[![iOS lobby](img/a17758825d092245a1eb2234e2451a13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iHwPssTZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/lobby.png)

我们可以通过使用 [PubNub 的调试控制台](https://www.pubnub.com/docs/console/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)或者运行井字游戏的 React 应用版本来测试该应用。React 应用程序已经连接到 React 本机应用程序，必要的逻辑已经处理完毕。你需要做的就是从 React 本地应用程序中插入相同的 Pub/Sub 键。

本节将分为两部分，详细介绍如何使用上述两种方法测试应用程序。对于每个部分，将有一个视频来回顾我们在该部分所做的事情。如果你更喜欢看视频而不是阅读，请转到该部分的结尾。

## 使用调试控制台玩游戏

在本节中，我们将使用调试控制台创建一个通道，并使用模拟器加入该通道。确保您已经在模拟器中打开了应用程序。

要进入调试控制台，进入 [PubNub 管理仪表板](https://admin.pubnub.com/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)并点击左边栏的**调试控制台**。

[![debug console](img/199bb5a26db8f673f3a61cc036463dc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pX4rZZmV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/debug_console.png)

在顶栏上，选择您用于此项目的应用程序和按键。

[![Select app and key](img/af43d5f95af57111ca8a6d39f0715a52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ekhqpGIm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/select_app_and_key.png)

您现在应该会看到一个面板，您可以在其中输入频道名称。将默认频道改为“*game lobby”*并添加客户端。

[![Debug Channel Panel](img/1e15907ed1a4f346397f5a74824b326a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3DCZ7kKD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/default_channel_panel.png)

创建客户端后，将默认消息替换为:

```
{"is_room_creator":true, "username":"Player X"} 
```

Enter fullscreen mode Exit fullscreen mode

您可以输入任何用户名，但是在这个例子中，我们将使用**玩家 X** 作为房间创建者，使用**玩家 O** 作为对手。点击*发送*按钮，你应该会在控制台看到消息。下面是调试控制台的屏幕截图:

[![Create a room in the debug console](img/68bf675e5c0d6b56e598de754210cf40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M7Eg-EWl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/debug-console-create-room-1.png)

接下来，我们必须为游戏创建一个新的渠道。转到我们使用的当前面板旁边的面板，将默认频道更改为*“tictactoe-game channel”*(**注**:虽然我们为游戏选择了固定的频道名称，但在应用中，如果玩家按下*创建*按钮，就会生成一个随机的频道名称)。

添加客户端以连接到新信道。回到我们发送第一条消息的前一个面板，取消订阅该频道，因为我们将不再使用它。

[![Connect to the game channel in the debug console](img/2141cf93da616772c84c90788c8664e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V4X5HI0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/connect-to-game-channel-debug-console.png)

现在，进入模拟器，输入玩家 O 的用户名**。点击“加入”按钮，输入频道名称“*game channel”*(注意:“*tictactoe-”*会追加到频道名称后面，所以可以不包含)。**

一旦你按下 **OK** ，大厅组件将被游戏组件替换，并显示桌面 UI。由于我们使用模拟器加入频道，您将无法在桌面上做任何事情，因为房间创建者会先采取行动。因此，在调试控制台上，将默认消息替换为:

```
{"row":0, "col":1, "piece":"X", "is_room_creator":true, "turn":"O"} 
```

Enter fullscreen mode Exit fullscreen mode

确保大写字母“X”和“O”。发送上述消息，您应该会在模拟器的表格上看到一个“X”。下面是调试控制台和模拟器的屏幕截图:

[![Gameplay for the debug console and the simulator](img/8e8285c096fb684d42eb076c70eba44a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kCUtefE---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/debug-console-simulator-gameplay.png)

现在你可以在模拟器中行动了。要继续玩，只需在调试控制台中更改**行**和**列**的值，当轮到房间创建者时，将“X”放置在桌子的不同部分。如果玩家赢了那一轮，获胜玩家的分数将在模拟器中更新。模拟器中的警告信息会通知对手等待新一轮。

[![End of the game alert for the opponent](img/5ec9683a3b70cd00cfb056c82d9ce332.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--faY7SjEa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/end-of-game-debug-console-simulator.png)

如果您想再玩一轮，请在调试控制台中键入以下消息:

```
{"reset":true} 
```

Enter fullscreen mode Exit fullscreen mode

上述消息将重置模拟器中的表格，进行新一轮。要结束游戏，请键入以下消息:

```
{"gameOver":true} 
```

Enter fullscreen mode Exit fullscreen mode

结束游戏会将对手送回大厅。

请观看下面的视频，回顾我们在本节中所做的工作。

[![Gameplay for the debug console and the simulator](img/8e8285c096fb684d42eb076c70eba44a.png)
T3】](http://www.youtube.com/watch?feature=player_embedded&v=i0DEuosFV0k)

您可能已经注意到，在使用调试控制台时有许多手动步骤。在应用程序本身，我们做的许多手动步骤都是自动化的，如取消订阅“*game lobby”*或订阅游戏的新频道。我们使用调试控制台是因为在您的机器上运行两个模拟器或仿真器可能会有些复杂。调试控制台是模拟应用程序游戏性的更好方法。

除了调试控制台，你还可以使用 React 版本的应用程序来模拟游戏。通过使用 React 应用程序，你不必担心手动运行命令，因为一切都将自动化，因为它应该在应用程序中。

## 使用 React 应用程序玩游戏

我们将使用模拟器创建一个通道，并使用 React 应用程序加入该通道(**注意**:React 应用程序当前设置为仅加入通道，而不创建通道)。你可以从这个[回购](https://github.com/ocastroa/react-tutorial-tic-tac-toe/)中克隆 React 应用。打开项目后，转到文件 *Game.js* ，在构造函数中，添加与 React 原生应用相同的**Pub/Sub 键。这就是 React 项目需要编辑的全部内容。**

[![Replace pub/sub keys](img/f270d8198a42ce42071bb152ce735afb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l4mChzaG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/replace_keys.png)

在终端中运行以下命令来安装依赖项:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

要运行该应用程序，请在终端中键入以下命令:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

该应用程序将在 [http://localhost:3000](http://localhost:3000) 中打开，其中有一个空表格和两个输入字段。

[![React app empty board](img/a8e8aed1a92b43974a009344ff7d6d1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bkzo8XYU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/empty-board-react-app-1024x491.png)

进入模拟器，在大厅中输入用户名 **Player X** ，然后按“创建”按钮创建一个新的房间 id。回到浏览器，在*用户名*字段，输入**玩家 O** 。在*房间名称*字段中，输入在模拟器中创建的房间 id。

[![join room React app](img/7699a384e2f34915c0c58e48ccd6b9f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IbNaKJ76--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/join-room-react-app.png)

一旦两个字段都被填写，按下*提交*按钮，游戏将为两个玩家开始。由于模拟器是房间的创建者，按桌子上的任意方块放置一个 **X** 。你应该在模拟器和浏览器中都可以看到 **X** 。尝试在模拟器的桌子上按下另一个方块，你会注意到，当轮到对手移动时，什么也没有发生。在浏览器中，按任意方块放置一个 **O** 。

[![React/ React Native gameplay](img/d7d1190e25126d73d0d845a71ac979b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SyZ7Lj8j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/react-app-gameplay.png)

继续玩，直到有人赢或游戏以平局结束。如果有获胜者，将在浏览器中宣布获胜者，同时获胜的方块背景颜色将变为绿色。在模拟器中，获胜玩家的分数将会更新，并且会有一个警告询问房间创建者他们是想再玩一轮还是退出游戏。

[![game over react app](img/94c36148a475e76500114a66943f1ef7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U6AYOUq0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/07/game-over-react-app.png)

如果房间创建者决定再玩一轮，模拟器和浏览器的表格都将重置为新一轮。如果房间创建者决定退出游戏，当浏览器重置桌子时，房间创建者将被带到大厅。您可以在模拟器中创建一个新频道，并在浏览器中加入该新频道。

请观看下面的视频，回顾我们在本节中所做的工作。

[![React/ React Native gameplay](img/d7d1190e25126d73d0d845a71ac979b8.png)
T3】](http://www.youtube.com/watch?feature=player_embedded&v=0W6OqKiP7GM)

## 接下来是什么

如果你想看更多关于如何在 React 原生项目中使用 PubNub 的例子，请查看我们所有的 [React 原生教程](https://www.pubnub.com/blog/tag/reactnative/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)。或者如果你想看看 PubNub 用于多人游戏的不同方式，请查看我们的[多人游戏教程](https://www.pubnub.com/blog/tag/multiplayer-gaming/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q3-DevTo-July-22)。