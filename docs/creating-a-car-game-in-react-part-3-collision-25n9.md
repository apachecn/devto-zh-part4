# 在 React 中创建汽车游戏-第 3 部分-碰撞

> 原文：<https://dev.to/pcmichaels/creating-a-car-game-in-react-part-3-collision-25n9>

在这个系列的第三篇文章中，我们将在游戏中加入碰撞。有关代码的完整列表，请参见[这里的](https://github.com/pcmichaels/ReactTransAm/tree/Part-Three)。

如果你对之前的帖子有疑问，请从这里开始[。](https://www.pmichaels.net/2019/06/03/creating-a-car-game-in-react-part-1-drawing-and-moving/)

既然我们引入了碰撞，我们也需要引入古老的游戏概念“生命”。这里的前提是，当你撞到什么东西的时候，你就失去了一条生命。

第一步是添加一个新的状态变量来保存玩家的剩余生命:

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
    gameLoopActive: false,
    message: ""
}; 
```

如果你看一下存储库，会发现有一点重构，我提取了一些 setState 代码，并将其分成逻辑函数。这里就不一一列举了。

# 冲突检出

在游戏循环结束时，我们现在有一个调用来检查我们是否与任何东西发生了碰撞:

```
if (this.detectAnyCollision()) {
    this.PlayerDies(); 
} 
```

碰撞检测代码非常简单，它基于一个简单的想法，即所有对象都可以被认为是矩形。虽然这并不精确，但对于我们的目的来说已经足够了:

```
detectAnyCollision() { 
        const halfWidth = this.spriteWidth / 2;
        const halfHeight = this.spriteHeight / 2;

        let rect1 = {x: this.state.playerX - halfWidth, y: this.state.playerY - halfHeight, 
            width: this.spriteWidth, height: this.spriteHeight}

        if (this.detectOutScreen(rect1)) {
            return true;
        }

        return this.obstacles.some(a => {
            var rect2 = {x: a.props.centreX - halfWidth, y: a.props.centreY - halfHeight, 
                width: this.spriteWidth, height: this.spriteHeight}

            if (this.detectCollision(rect1, rect2)) {
                return true;
            } else {
                return false;
            }
        });
}

detectCollision(rect1, rect2) {
    if (rect1.x < rect2.x + rect2.width &&
    rect1.x + rect1.width > rect2.x &&
    rect1.y < rect2.y + rect2.height &&
    rect1.y + rect1.height > rect2.y) {
        return true;
    }
    return false;
}

detectOutScreen(rect1) {
    if (rect1.x < 0 || rect1.x + rect1.width > this.state.windowWidth
    || rect1.y < 0 || rect1.y + rect1.height > this.state.windowHeight) {
        return true;
    }
    return false;
} 
```

碰撞检测代码本身是从[这里](https://developer.mozilla.org/en-US/docs/Games/Techniques/2D_collision_detection)被窃取的。正如你所看到的，我们所做的只是将我们的对象转换成矩形，然后查看它们是否彼此相交，或者玩家是否已经离开了游戏区域。

## 关于 forEach 和一些

我最初使用的。forEach for detectany collision()代码。虽然这对于 C#程序员来说是有意义的，但事实上，Javascript 版本的代码确实如它在 tin 上所说的那样；也就是对每个元素都执行，没有办法提前退出！

# 玩家死亡并得分

既然我们引入了碰撞，我们就应该考虑当它发生时该怎么办。一个游戏中通常的事情是玩家要么“死亡”，要么他们失去“健康”。由于这是受一个频谱游戏的启发，我们将选择“死亡”。您之前已经看到，我们引入了“生命”的概念，因为它是一个谱，所以它必须是 3！

处理玩家死亡的代码是:

```
PlayerDies() { 
    this.setState({
        playerLives: this.state.playerLives - 1,
        gameLoopActive: false
    });
    if (this.state.playerLives <= 0) {
        this.initiateNewGame();
    } else {
        this.resetCarPosition();
    }
    this.repositionPlayer();
    this.setState({ 
        gameLoopActive: true
    });
} 
```

只是快速提醒一下，这不是一个完整的代码清单——请查看 GitHub 资源库；然而，除了生命的减少，这里最重要的是 gameLoopActive 代码。

这里的想法是，我们只在这个状态变量被设置时执行游戏循环；这意味着我们可以在处理玩家碰撞时停止游戏循环。

游戏循环代码的变化非常简单:

```
gameLoop() {
    if (!this.state.gameLoopActive) return;

 . . . 
```

# 撞坏的汽车

一切都很好，但就其现状而言，这只是导致汽车在撞到树上时停下来，然后被重新定位。我们可以通过添加一个小的“动画”来指示崩溃来解决这个问题。如果你看一下这里的，你就会明白为什么我的图形赢得了几个奖项！

为了插入这个插件，我们将更改汽车图形绑定:

```
render() { 
    return <div onKeyDown={this.onKeyDown} tabIndex="0">
    <GameStatus Lives={this.state.playerLives} Message={this.state.message}/>
    <Background backgroundImage={backgroundImg}
     windowWidth={this.state.windowWidth} windowHeight={this.state.windowHeight} /> 

    <Car carImage={this.state.playerCrashed ? brokenCarImg : carImg} 
 centreX={this.state.playerX} centreY={this.state.playerY} 
 width={this.spriteWidth} height={this.spriteHeight} 
 rotation={this.state.playerRotation} /> 

    {this.obstacles} 
    </div>
} 
```

所以，在设置崩溃标志的地方，我们绑定到 brokenCarImg 否则为 carImg 它们在顶部定义:

```
import carImg from '../Assets/Car.png';
import brokenCarImg from '../Assets/Crash.png'; 
```

我们还将 playerDies()函数一分为二:

```
playerDying(tillDeath) {
    this.setState({
        playerCrashed: true,
        gameLoopActive: false
    });
    this.stopCar();
    setTimeout(this.playerDies.bind(this), tillDeath);
}

playerDies() { 
    this.setState({
        playerLives: this.state.playerLives - 1,
        gameLoopActive: false
    });
    if (this.state.playerLives <= 0) {
        this.initiateNewGame();
    } else {
        this.resetCarPosition();
    }
    this.repositionPlayer();
    this.setState({ 
        playerCrashed: false,
        gameLoopActive: true
    });
} 
```

我们在这里所做的只是调用第一个函数，这实际上只是改变图像，然后在超时时调用第二个函数。还是那句话，调用 timeout 的时候不要忘了`.bind()`，否则无法访问`this`！

# 脚注

*我实际上并没有赢得任何图形方面的奖项——不过，我骗过了你！

# 参考

[https://developer . Mozilla . org/en-US/docs/Games/Techniques/2D _ 碰撞 _ 检测](https://developer.mozilla.org/en-US/docs/Games/Techniques/2D_collision_detection)

[https://stack overflow . com/questions/34653612/what-does-return-keyword-mean-inside-foreach-function/34653650](https://stackoverflow.com/questions/34653612/what-does-return-keyword-mean-inside-foreach-function/34653650)

[https://medium . com/@ Benjamin cherion/how-to-break-a-array-in-JavaScript-6 D3 a55 BD 06 f 6](https://medium.com/@benjamincherion/how-to-break-an-array-in-javascript-6d3a55bd06f6)