# 在 React 中创建汽车游戏-第 2 部分-转向和障碍

> 原文：<https://dev.to/pcmichaels/creating-a-car-game-in-react-part-2-steering-and-obstacles-2c0l>

在这个系列的第二部分，我们将为我们的汽车增加一些转向，并引入障碍物的概念。如果你想知道这是什么的第二部分，请从这里开始。

这篇文章的 GitHub 报告可以在这里找到。

在本系列的第一篇文章中，我们添加了一辆汽车，并允许用户以一种非常粗糙的方式在屏幕上移动它；在这里，我们将改变控制，感觉更像一辆汽车。与前一篇文章一样，不是每一个变化都会在这里，但它会在上面的 GitHub repo 中。

# 转向和加速

让我们来看看游戏组件中的控件:

onKeyDown 现在看起来有点不同:

```
onKeyDown(e) {
switch (e.which) {
    case 37: // Left
        this.playerSteer(-10);
        break;
    case 38: // Up
        this.playerAccelerate(0.3);
        break;
    case 39: // Right
        this.playerSteer(10);
        break;
    case 40: // Down
        this.playerDecelerate(-0.5);
        break;
    default:
        break;
}
} 
```

所以，我们不再简单地重新定位汽车，而是对它施加力。最初，我把 Down 简单地理解为负加速度，意思是如果你用力过猛，就会向后倒！这是上面提到的三个函数:

```
playerAccelerate(speed) {
this.setState({
    playerMomentum: this.state.playerMomentum + speed
});
}

playerDecelerate(speed) {
if (this.state.playerMomentum > 0) {
    this.setState({
        playerMomentum: this.state.playerMomentum + speed
    });
} else if (this.state.playerMomentum < 0) {
    this.setState({
        playerMomentum: this.state.playerMomentum - speed
    });
}
}

playerSteer(direction) {
this.setState({
    playerRotation: this.state.playerRotation + direction
});
} 
```

有许多新的状态变量，我不会在这里列出。然而，因为我们不再基于用户动作重新定位汽车，我们需要应用我们正在改变的力；也就是说，我们需要一个游戏循环。

# 游戏循环

游戏循环代码相对复杂。看到这里，你应该会认真考虑使用某种游戏引擎:

```
gameLoop() { 
const radians = (this.state.playerRotation - 90) * Math.PI / 180; 
const aX = (this.state.playerMomentum * Math.cos(radians));
const aY = (this.state.playerMomentum * Math.sin(radians));
const velocityX = this.state.playerVelocityX;
const velocityY = this.state.playerVelocityY;
const velocitySq = Math.pow(velocityX, 2) + Math.pow(velocityY, 2);
const posSq = Math.pow(aX, 2) + Math.pow(aY, 2);
const velocityPosSq = Math.pow(velocityX * aX + velocityY * aY, 2);
let skidFactor = (posSq == 0 || velocitySq == 0) ? 0 : 1 - (velocityPosSq / posSq / velocitySq);

if (skidFactor <= 0) skidFactor = 0; 

this.setState({
    playerVelocityX: (skidFactor * velocityX) + ((1 - skidFactor) * aX),
    playerVelocityY: (skidFactor * velocityY) + ((1 - skidFactor) * aY)
}); 
this.playerMove(
    this.state.playerX + this.state.playerVelocityX,
    this.state.playerY + this.state.playerVelocityY 
);
this.playerDecelerate(-(0.1 + skidFactor));
} 
```

如果你想知道这种令人头疼的数学知识来自哪里，请看这里。

我把它分开了，以使它稍微更容易理解，但前提是，如果你快速行驶并突然改变方向，它不会立即转向。再一次，如果你认为你不想被这种事情所困扰，那么很多游戏引擎会为你解决这个问题。

# 障碍

最后，我们有我们的障碍。目前还没有冲突，但这基本上是把树的图片放在屏幕周围(顺便说一下，我自己做了所有的艺术作品，我认为读者会对这些留下深刻的印象！)我们稍后将回到这一点，以使碰撞工作:

```
buildObstacles() {
let obstacles = [];
const obstacleCount = Math.floor(Math.random() * 10) + 1;
console.log('Obstacle count ' + obstacleCount);
for (let i = 1; i <= obstacleCount; i++) {
    const centreX = Math.floor(Math.random() * this.state.windowWidth) + 1;
    const centreY = Math.floor(Math.random() * this.state.windowHeight) + 1;

    obstacles.push(<Obstacle key={i} image={treeImg} centreX={centreX} centreY={centreY} width={this.spriteWidth} height={this.spriteHeight} />);
}
return obstacles;
} 
```

这个函数所做的就是建立一个 HTML 数组；然后，我们将其输入到构造函数中的一个类变量中:

```
constructor(props) {
super(props);
document.body.style.overflow = "hidden";
this.state = {
    playerX: 100,
    playerY: 100,
    windowWidth: 1500,
    windowHeight: 1500,
    playerMomentum: 0,
    playerRotation: 0,
    playerVelocityX: 0,
    playerVelocityY: 0
};
this.spriteWidth = 25;
this.spriteHeight = 25;
this.updateWindowDimensions = this.updateWindowDimensions.bind(this);
this.onKeyDown = this.onKeyDown.bind(this); 
this.obstacles = this.buildObstacles(); 
} 
```

(顺便说一下，值得注意的是，我们通过设置 document.body.style.overflow 来防止滚动)

最后，我们将在 render 方法中显示它:

```
render() { 
return <div onKeyDown={this.onKeyDown} tabIndex="0">
    <Background backgroundImage={backgroundImg}
    windowWidth={this.state.windowWidth} windowHeight={this.state.windowHeight} /> 

    <Car carImage={carImg} centreX={this.state.playerX} 
    centreY={this.state.playerY} width={this.spriteWidth}
    height={this.spriteHeight} rotation={this.state.playerRotation} /> 

    {this.obstacles} 
</div>
} 
```

这种技术允许您动态构建 HTML 对象数组。这里要注意的是我们正在通过的“钥匙”;如果您没有通过这一步，您将开始得到以下错误:

> 列表中的每个孩子都应该有一个唯一的“关键”道具。

在下一篇文章中，我们将介绍碰撞。

# 参考

[https://stack overflow . com/questions/5192983/calculating-x-y-movement-based-on-rotation-angle](https://stackoverflow.com/questions/5192983/calculating-x-y-movement-based-on-rotation-angle)

[https://game dev . stack exchange . com/questions/172571/how-to-implement-a-light-skid-in-a-car-game](https://gamedev.stackexchange.com/questions/172571/how-to-implement-a-slight-skid-in-a-car-game)

[https://stack overflow . com/questions/39962757/prevent-scrolling-using-CSS-on-react-rendered-components](https://stackoverflow.com/questions/39962757/prevent-scrolling-using-css-on-react-rendered-components)

原贴[此处](https://www.pmichaels.net/2019/06/07/creating-a-car-game-in-react-part-2-steering-and-obstacles/)。