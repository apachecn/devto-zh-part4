# 在 React 中创建汽车游戏-第 4 部分-分数

> 原文：<https://dev.to/pcmichaels/creating-a-car-game-in-react-part-4-score-59n5>

这是一个系列的第四部分(开始于[这里](https://www.pmichaels.net/2019/06/03/creating-a-car-game-in-react-part-1-drawing-and-moving/))。到目前为止，我们有一个游戏，你可以在屏幕上嗖嗖地避开树。这有效地包含了这种类型游戏的大多数方面；也就是说，你可以移动，但有什么东西阻止了你。

下一步是为玩家介绍一些尝试去做的事情，并在此基础上给出一个分数。在我们的例子中，我们的玩家将尝试收集杯子——按照原来的游戏。我为杯子创建的资产在这里是。对于任何关注这个系列的人来说，你可能已经注意到我的作品有点...废话。

这个帖子的出处是[这里](https://github.com/pcmichaels/ReactTransAm/tree/Part-Four)。

# 杯子

我们在这里做的第一件事是添加一些代码，在屏幕周围放置一些杯子；代码基本上与建造障碍物相同:

```
placeCups() {
    let cups = [];
    const cupCount = 1;
    for (let i = 1; i <= cupCount; i++) {
        const centreX = Math.floor(Math.random() * this.state.windowWidth) + 1;
        const centreY = Math.floor(Math.random() * this.state.windowHeight) + 1; 
        cups.push(<GameItem key={i} image={cupImg} centreX={centreX} centreY={centreY} width={this.spriteWidth} height={this.spriteHeight} itemType={2} />);
    }
    return cups;
} 
```

在后面的帖子中，我希望做一个完整的重构，但现在，我们有一个单独的功能。这与障碍物的渲染方式相同:

```
render() { 
    return <div onKeyDown={this.onKeyDown} tabIndex="0">
        <GameStatus Lives={this.state.playerLives} Message={this.state.message} Score={this.state.score} />

        <Background backgroundImage={backgroundImg}
    windowWidth={this.state.windowWidth} windowHeight={this.state.windowHeight} /> 

        <Car carImage={this.state.playerCrashed ? brokenCarImg : carImg} 
    centreX={this.state.playerX} centreY={this.state.playerY} 
    width={this.spriteWidth} height={this.spriteHeight} 
    rotation={this.state.playerRotation} /> 

        {this.obstacles} 

        {this.cups} 
    </div>
} 
```

# 收集杯子

为了收集东西，玩家必须与它碰撞。我们需要稍微修改一下碰撞代码，使其更易于重用:

```
detectAnyCollision(rect1) { 
    // Have we crashed or left the screen
    if (this.detectOutScreen(rect1)) {
        return true;
    }
    let collided = this.detectGameItemCollision(this.halfWidth, this.halfHeight, rect1, this.obstacles);
    if (collided !== undefined) {
        return true;
    }
    return false;
}

detectGameItemCollision(halfWidth, halfHeight, rect1, gameItemList) {
    const collided = gameItemList.find(a => {
        var rect2 = {
            x: a.props.centreX - halfWidth, y: a.props.centreY - halfHeight,
            width: this.spriteWidth, height: this.spriteHeight
        };
        return (this.detectCollision(rect1, rect2));
    }); 
    return collided;
} 
```

正如你所看到的，我们现在有一个函数返回我们碰撞的物体，而不是一个简单的布尔值。然后，我们在游戏循环结束时使用它来确定我们是否与杯子相撞:

```
// Check for collected cup
const item = this.detectGameItemCollision(this.halfWidth, this.halfHeight, rect1, this.cups);
if (item !== undefined) {
    this.collectedCup(item.key);
} 
```

# 得分

如果没有永久的记录，收集杯子就没有什么意义，所以我们需要增加一个分数。让我们从 game.jsx 中的一个状态变量开始:

```
this.state = {
    playerX: 100,
    playerY: 100,
    windowWidth: 1500,
    windowHeight: 1500,
    playerMomentum: 0,
    playerRotation: 0,
    playerVelocityX: 0,
    playerVelocityY: 0,
    playerLives: 3,
    playerCrashed: false,
    gameLoopActive: false,
    message: "",
    score: 0
}; 
```

这是我们之前提到的 collectedCup 函数:

```
collectedCup(key) {
    this.setState({ score: this.state.score + 1 });
    this.cups = this.cups.filter(cup => cup.key != key);
    this.updateMessage("Collected cup");
} 
```

我们在这里所做的只是简单地更新分数，然后从列表中删除该奖杯。

最后一部分是在屏幕上显示分数；让我们来看看更新后的 GameStatus.jsx:

```
function GameStatus(props) {
    const flexStyle = {
        display: 'flex',
        position: 'absolute',
        zIndex: 1,
        margin: 20,
        justifyContent: 'center',
        alignItems: 'center',
        width: '100%'
    };
    const labelStyle = { 
        zIndex: 1,
        margin: 50
    };
    return ( 
        <div className="flex-container" style={flexStyle}>
            <label style={labelStyle}>
                Lives Remaining: {props.Lives}
            </label>
            <label style={labelStyle}>
                Score: {props.Score}
            </label>
            <label style={labelStyle}>
                {props.Message}
            </label>
        </div> 
    );
} 
```

如您所见，我们只是将分数显示为状态的一部分。

在下一篇文章中，我们将看看等级的概念，并介绍一个时间限制。

# 参考

[https://www.w3schools.com/css/css3_flexbox.asp](https://www.w3schools.com/css/css3_flexbox.asp)

这篇文章的原始版本可以在这里找到[。](https://www.pmichaels.net/2019/06/24/creating-a-car-game-in-react-part-4-score/)