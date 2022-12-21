# 在 React 中创建汽车游戏-第 1 部分-绘制和移动

> 原文：<https://dev.to/pcmichaels/creating-a-car-game-in-react-part-1-drawing-and-moving-1n7c>

自从我开始研究 React，我就想知道是否有可能创造一个多用户游戏。这个游戏看起来有点像我以前玩过的一个叫 Trans-Am 的频谱游戏。我猜大多数读这篇文章的人都不会老到记得这些。本质上，它标志着汽车游戏发展的巅峰，从此一切都走下坡路了。

如果你不知道我在说什么，这里有一个游戏的演示。

我不打算完全模仿这一点，我想我会用它作为一个多玩家汽车游戏的基础。

这篇文章的 GitHub 库可以在这里找到。

# 创建 React 应用程序

我们将从创建一个新的 React 应用程序开始(详情见[此处](https://www.pmichaels.net/2019/01/14/a-c-developers-guide-to-reactjs-part-1-create-run/)):

[![](img/9643d587ba424bf3b2f6e17c5be8c25f.png)](https://www.pmichaels.net/wp-content/uploads/2019/06/React-game-1.png)

现在我们有了应用程序，我们需要一些游戏资产。如果你想和我使用相同的资产，那么请随意调用我的库。然而，在这个阶段，你需要的只是一个方形盒子和一个绿色屏幕。

# 游戏布局

下一阶段是设计游戏布局；因为这是 React，我们将从 App.js 开始。我们将把所有游戏逻辑委托给一个名为 game 的组件:

```
import React from 'react';
import './App.css';
import Game from './Components/Game';
function App() {
return (
    <div className="App">
        <Game />
    </div>
    );
}
export default App; 
```

如果你想看，全面什么游戏。Jsx 长什么样，那就看看 GitHub 上的最新版本[。但是，一些亮点是渲染方法:](https://github.com/pcmichaels/ReactTransAm/blob/master/src/Components/Game.jsx)

```
render() { 
return <div onKeyDown={this.onKeyDown} tabIndex="0">
    <Background backgroundImage={backgroundImg}
    windowWidth={this.state.windowWidth} windowHeight={this.state.windowHeight} /> 
<Car carImage={carImg} centreX={this.state.playerX} 
    centreY={this.state.playerY} width={this.playerWidth}
    height={this.playerHeight} /> 
</div>
} 
```

这可能会随着游戏的进展而改变，但目前，它只是呈现给两个组成部分。我们在这里也响应 KeyDown，所以让我们看看 onKeyDown:

```
onKeyDown(e) {
switch (e.which) {
    case 37: // Left
        this.playerMove(this.state.playerX - this.SPEED, this.state.playerY); 
        break;
    case 38: // Up
        this.playerMove(this.state.playerX, this.state.playerY - this.SPEED);
        break;
    case 39: // Right
        this.playerMove(this.state.playerX + this.SPEED, this.state.playerY); 
        break;
    case 40: // Down
        this.playerMove(this.state.playerX, this.state.playerY + this.SPEED);
        break;
    default:
        break;
}

playerMove(x, y) {
this.setState({
    playerX: x,
    playerY: y
}); 
} 
```

我们将玩家的位置存储在 state 中；正如我在这里详述的，这使我们能够更新状态，并让 React 在检测到虚拟 DOM 中的变化时更新屏幕。

# 游戏组件

为了尽可能接近 React 的首选架构，游戏的组件(目前是背景和汽车)将是组件。背景很简单:

```
import React from 'react';
function Background(props) {
const bgStyle = { 
    width: `calc(${props.windowWidth}px)`, 
    height: `calc(${props.windowHeight}px)`, 
    top: 0,
    left: 0,
    position: 'absolute' 
};
return (
    <img src={props.backgroundImage} style={bgStyle} />
);
}
export default Background; 
```

我们基本上只是在这里展示一幅图像。值得注意的一点是，windowWidth 和 windowHeight 是属性，而不是状态。它们在游戏组件中作为状态存在，当它们改变时，在那里更新，在这里更新。React 的家伙把这个叫做[提升状态](https://reactjs.org/docs/lifting-state-up.html)。

汽车部件是完全相同的想法:

```
import React from 'react';
function Car(props) {
const left = Math.round(props.centreX - (props.width / 2));
const top = Math.round(props.centreY - (props.height / 2));
const carStyle = { 
    width: `calc(${props.width}px)`, 
    height: `calc(${props.height}px)`, 
    top: `calc(${top}px)`,
    left: `calc(${left}px)`, 
    position: 'absolute',
    zIndex: 1 
};
return (
    <img src={props.carImage} style={carStyle} />
);
}
export default Car; 
```

这种在更高的组件中维护状态的想法有许多优点；例如，通过这种方式，您可以在组件之间共享单个状态；然而，对我们来说，最大的优势是，虽然组件实际上是智能精灵，但您可以轻松地创建汽车组件的“敌人汽车”版本。

值得记住的是，因为汽车的位置在这个组件中不存在，即使我们想改变它，我们也不能在这里改变它。解决这个问题的策略是将一个更新函数作为属性传入(实际上是一个函数指针，您可以从子组件中调用它)。

在下一篇文章中，我将更新这个动作，使它更像汽车，并引入一些障碍物。

# 参考

[https://reactjs.org/docs/components-and-props.html](https://reactjs.org/docs/components-and-props.html)

[https://stack overflow . com/questions/43503964/onkeydown-event-not-working-on-divs-in-react](https://stackoverflow.com/questions/43503964/onkeydown-event-not-working-on-divs-in-react)

[https://stack overflow . com/questions/37440408/how-to-detect-ESC-key-press-in-react-and-how-to-handle-it/46123962](https://stackoverflow.com/questions/37440408/how-to-detect-esc-key-press-in-react-and-how-to-handle-it/46123962)

[https://reactjs.org/docs/lifting-state-up.html](https://reactjs.org/docs/lifting-state-up.html)

[https://stack overflow . com/questions/36862334/get-viewport-window-height-in-react js](https://stackoverflow.com/questions/36862334/get-viewport-window-height-in-reactjs)

原贴[此处](https://www.pmichaels.net/2019/06/03/creating-a-car-game-in-react-part-1-drawing-and-moving/)。