# 在 React 中创建汽车游戏-第 5 部分-级别和时间

> 原文：<https://dev.to/pcmichaels/creating-a-car-game-in-react-part-5-levels-and-time-46e>

这是一个系列的第五部分(开始于[这里](https://www.pmichaels.net/2019/06/03/creating-a-car-game-in-react-part-1-drawing-and-moving/))。

在上一篇帖子中，我们加入了分数的概念。汽车现在可以在避开树的同时收集杯子；然而，我们对没有杯子时会发生什么没有任何概念。

在本帖中，我们将为游戏增加关卡，这样当你收集到所有的杯子后，你就可以升级了。我们还将引入一个时间限制，使它变得越来越难(就目前的情况来看，收集杯子并不是一个很大的挑战，因为你可以花一整天的时间)。

这个帖子的出处是[这里](https://github.com/pcmichaels/ReactTransAm/tree/Part-Five)。同样，不是所有的东西都在帖子里，所以请参考知识库。

# 级别

因为我们正在创建级别，所以我们需要跟踪我们所在的级别，所以一个新的状态属性是合适的:

```
this.state = {
    playerX: 100,
    playerY: 100,
    windowWidth: window.innerWidth,
    windowHeight: window.innerHeight,
    playerMomentum: 0,
    playerRotation: 0,
    playerVelocityX: 0,
    playerVelocityY: 0,
    playerLives: 3,
    playerCrashed: false,
    gameLoopActive: false,
    message: "",
    score: 0,
    level: 1,
    cupCount: 1, 
    remainingTime: 0
}; 
```

如果你从第一篇文章就开始关注这个问题，你可能会问自己:“他会重构并清理这个问题吗？?"

对此我自信地回应道:

“大概吧！”

无论如何，你会注意到我们有级别，分数，时间和杯数。从概念上来说，升级只是一个数字；下面是完成一关的代码:

```
completedLevel() {
    if (this.state.level >= 10) {
        this.updateMessage("Congratulations, you've completed the game");
    } 
    this.startLevel(this.state.level + 1);
} 
```

startLevel 是一个轻微的重构，它本质上将杯子计数和等级设置为新值——我们稍后将回到这个问题。

您只能通过收集足够的杯子来完成一个级别，因此触发器应该在杯子收集中:

```
collectedCup(key) {
    this.setState({ 
        score: this.state.score + 1 
    });
    this.cups = this.cups.filter(cup => cup.key != key);
    this.updateMessage("Collected cup");
    if (this.cups.length == 0) {
        this.completedLevel();
    } 
} 
```

一旦我们降到 0 杯，我们就称之为完全水平。

# 时间

现在是时候看看起始级别代码了:

```
startLevel(level) { 
    this.setState({
        level: level,
        cupCount: level * 2 
    }); 
    this.obstacles = this.buildObstacles(); 
    this.cups = this.placeCups();
    this.resetCarPosition();
    this.totalLevelTimeMS = (this.TOPLEVEL - (this.state.level - 1)) * 60 * 1000
    let startLevelTimeMS = (new Date()).getTime();
    this.endLevelTimeMS = startLevelTimeMS + this.totalLevelTimeMS; 
} 
```

我们正在计算用户何时超时，并将其存储在 endLevelTime 中。请注意，这些都不在状态变量中——唯一的状态变量在游戏循环中更新:

```
let remaining = (this.endLevelTimeMS - (new Date()).getTime()) / 1000;
if (remaining <= 0) {
    this.updateMessage("Out of time!");
    this.playerDies();
}
this.setState({
    remainingTime: Math.round(remaining)
}); 
```

这是在游戏循环的最后:我们正在更新 remainingTime 状态变量，但首先，我们计算它，如果它是零，玩家就会死亡(失去一条生命)。

我们需要调整玩家死亡的代码，因为否则计时器将永远不会重置:

```
playerDies() { 
    this.setState({
        playerLives: this.state.playerLives - 1,
        gameLoopActive: false
    });
    if (this.state.playerLives <= 0) {
        this.initiateNewGame();
    } else {
        this.startLevel(this.state.level);
    }
    this.repositionPlayer();
    this.setState({ 
        playerCrashed: false,
        gameLoopActive: true
    });
} 
```

最后一部分是用我的另一个专利图标让时间看起来好一点。GameStatus.jsx 现在应该返回以下内容:

```
return (      
    <div className="flex-container" style={flexStyle}>
        <label style={labelStyle}>
            Lives Remaining: {props.Lives}
        </label>
        <label style={labelStyle}>
            Score: {props.Score}
        </label>
        <label style={labelStyle}>
            Level: {props.Level}
        </label>            

        <div style={containerStyle}>  
            <img src={clockImg} style={imgStyle} />
            <div style={textDivStyle}>{props.RemainingTime}</div>
        </div>

        <label style={labelStyle}>
            {props.Message}
        </label>
    </div>  
); 
```

这里有一些新的样式，使时间出现在[时钟图标](https://github.com/pcmichaels/ReactTransAm/blob/Part-Five/src/Assets/Clock.png)上:

```
const containerStyle = {
    position: 'relative',
    textAlign: 'center',
    color: 'red'
}

const textDivStyle = {        
    position: 'absolute',
    top: '50%',
    left: '50%',
    transform: 'translate(-50%, -50%)',
    zIndex: 1,
    fontWeight: 'bold'
}

const imgStyle = {
    width: '100%',
    zIndex: 0
} 
```

在下一部分，我们将实现一个高分表。

这个本来贴[这里](https://www.pmichaels.net/2019/06/30/creating-a-car-game-in-react-part-5-levels-and-time/)。