# HTML 画布|很漂亮

> 原文：<https://dev.to/karx_brb/html-canvas-it-s-beautiful-4hha>

# HTML 画布——为什么没有得到充分利用？

要回答这个问题，我们必须真正了解 HTML Canvas 的现状。

# 什么是 HTML 画布？

如果你曾经尝试过在网页上“画图”，搜索引擎给你的第一件东西就是 HTML 画布。这就是 2019 年 HTML 画布的定义。
MDN 中的定义:

> Canvas API 提供了一种通过 JavaScript 和 HTML \'
> 
> <canvas>\ '元素绘制图形的方法。除此之外，它还可以用于动画、游戏图形、数据可视化、照片处理和实时视频处理。
> Canvas API 主要关注 2D 图形。WebGL API 也使用\' <画布> \ '元素，绘制硬件加速的 2D 和 3D 图形</canvas>

## 你为什么没有用过 HTML Canvas？

因为开发者不推荐它。大部分时间。投资回报率几乎不值得，但对游戏开发者来说不值得。

如果你曾经在网络游戏上工作过，那么你就是那些真正见过 Canvas APIs 的少数人之一。那我们就开始吧。

## 入门。

我注意到的第一件事是，可以专门使用 CSS、JS 或 HTML 中的任何一种来设置画布的宽度和高度(我们将讨论为什么会出现这种分歧)。我选择用 CSS 给他们这些。100vw，100vh。简单的
然后，画了一条线。画了一个球。
像素密度似乎很低。

> 输入:[window . devicepixelrratio](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio)
> 让我们设置一个函数 setupCanvas 来封装所有这些。

```
setupCanvas(canvasDomRef) {
        // Get the device pixel ratio, falling back to 1.
        const canvas = document.querySelector(canvasDomRef);
        var dpr = window.devicePixelRatio || 1;
        // Get the size of the canvas in CSS pixels.
        var rect = canvas.getBoundingClientRect();
        // Give the canvas pixel dimensions of their CSS
        // size * the device pixel ratio.
        canvas.width = rect.width * dpr;
        canvas.height = rect.height * dpr;
        this.width = canvas.width;
        this.height = canvas.height;
        this.ctx = canvas.getContext('2d');
        // Scale all drawing operations by the dpr, so you
        // don't have to worry about the difference.
        this.ctx.scale(dpr, dpr);
        return this.ctx;
      } 
```

还有，现在是 2019 年。在那里有一个静态图像并不有趣。我们会画画！

> Enter: [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)

对于使用请求动画帧，最简单的方法是这样做:

```
...

function draw() {
    // this is where we will put our logic for eachcycle

    requestAnimationFrame(draw);
}

draw()

... 
```

让我们用这个结构来完成这个入门课程，我将用它来玩画布。

```
.
├── project                 
│   ├── kaaro.html          # the html file with <canvas>
│   ├── kaaro.css           # Styles anyone?
│   └── kaaro.js            # Our main JS file - with draw() function
├── utils                    
│   └── kCanvas.js          # Reusable Canvas like our setupCanvas function
└── ...                     # Anything else.. or maybe I guess this file 
```

## 越来越舒服

我觉得所有的文档都应该有一个“变得舒适”的标题，这是可选的。为此，我们制作了一个小游戏。一款 block breaker 游戏(pr 突围游戏)推荐精准。

我跟随这个 MDN 教程来到这里:[https://developer . Mozilla . org/en-US/docs/Games/Tutorials/2D _ Breakout _ game _ pure _ JavaScript](https://developer.mozilla.org/en-US/docs/Games/Tutorials/2D_Breakout_game_pure_JavaScript)
强烈推荐。
我完成后的版本正在尝试 2 中。我希望在这方面多下功夫。

旁注:现在也许是时候写下这一页了:[https://developer.mozilla.org/en-US/docs/Web](https://developer.mozilla.org/en-US/docs/Web)
这一页是圣经。称之为假设，称之为开放，或者称之为工具。这一页是圣经。

## 我们的吉祥物:朗顿的蚂蚁

[兰顿蚂蚁](https://en.wikipedia.org/wiki/Langton%27s_ant)

我们将使用 Langtos 的蚂蚁作为我们用来了解的吉祥物，并了解 Canavs。(一只困在我们自己的 2D 宇宙中的蚂蚁)

如果你去维基，这是一个兔子洞，但你会很乐意沉迷其中。有趣的是，我敢打赌你会在那里找到一个或多个链接，你可能会因为超出本文范围的原因而深深地连接上(比如塑造你是谁的链接:P)。对我来说是《我的世界》。

对于那些聪明地避免进入那个兔子洞的人，啊，我将试着为我们的上下文做一个总结。

我们有一个巨大的网格，我们的 html 画布，蚂蚁的宇宙。我们为这只“蚂蚁”定义了一个起始位置，然后我们就有了一套规则来描述这只蚂蚁在这个世界上的行为。
每个网格位置都是彩色的，非黑即白。

规则:

*   如果蚂蚁在白色位置。它会把它涂成黑色，右转 90 度，移动 1 个单位。
*   如果蚂蚁在黑色位置。它会把它涂成白色，向左转 90 度，移动 1 个单位。

这些简单的规则导致了惊人的行为，我们现在将在我们的世界，HTML 画布上模拟这些行为。

## 模拟 1

尝试 3 -回购的一部分

让我们从使用我们的工具
创建一个画布开始

```
import { kCanvas } from "../utils/mycanvas.js";

var kC = new kCanvas("#myCanvas"); 
```

接下来，让我们为我们的 Langtons ant 创建一个类来保持事物的正式性。

```
class LangtonAntGrid {

    constructor() {

    }

    init(x = 20, y =20) {       
        this.grid = [];
        this.max_x = x;
        this.max_y = y;
        this.currentPosition = Object.assign({}, {
            x: this.max_x/2,
            y: this.max_y/2,
            color: 0,
            heading: 0
        });
        for (let i=0; i<x; i++) {
            this.grid[i] = [];
            for (let j=0; j<y; j++) {
                this.grid[i][j] = Object.assign({}, {
                    color: 0
                });
            }
        }
    }
    updateGrid() {
        const currentStatus = Object.assign({}, this.currentPosition);

        console.log(currentStatus);
        // first update current box
        this.currentPosition.color = (this.currentPosition.color + 1)%2;
        this.grid[this.currentPosition.x][this.currentPosition.y].color = this.currentPosition.color;
        this.drawPosition(this.currentPosition);

        //move to next Box
        if (currentStatus.color === 0) {
            this.currentPosition.heading = (this.currentPosition.heading + 1)%4;
            console.log('right');
        } else if (currentStatus.color === 1) {
            this.currentPosition.heading = (this.currentPosition.heading + 3)%4;
            console.log('left');
        }

        switch(this.currentPosition.heading) {
            case 0: this.currentPosition.y--;
                break;
            case 1: this.currentPosition.x++;
                break;
            case 2: this.currentPosition.y++;
                break;
            case 3: this.currentPosition.x--;
                break;
        }
        this.currentPosition.color = this.grid[this.currentPosition.x][this.currentPosition.y].color;
        console.log(this.currentPosition);

    }

    getLog() {
        console.log(this.grid);
    }
    drawPosition(position) {
        kC.drawBlock(position.x, position.y, position.color);    
    }
} 
```

现在运行它，让我们创建一个实例，并使用 requestAnimationFrame 循环调用它。

```
 var antGrid = new LangtonAntGrid();
antGrid.init(500,500);

kC.drawGrid(500,500, false);

function draw() {

    antGrid.updateGrid();

    requestAnimationFrame(draw);
}

draw(); 
```

如果你正在跟随，现在应该是你真正了解蚂蚁的简单性和普遍性的时候了。

试着玩玩蚂蚁可以拥有的规则。它可以有两种以上的规则，两种以上的颜色，如果你真的准备好了，还可以有两个以上的尺寸。
更新构造函数()以引入 2 个属性，并更新 updateGrid()函数以使用这 2 个属性，从而使更多的规则成为可能。

```
class LangtonAntGrid {

    constructor() {
        this.numberOfStates = 4;
        this.stateTransitions = ['R', 'L', 'L ', 'R'];
    }
    ...
    ...
    updateGrid() {
        const currentStatus = Object.assign({}, this.currentPosition);

        this.currentPosition.color = (this.currentPosition.color + 1)%(this.numberOfStates);
        this.grid[this.currentPosition.x][this.currentPosition.y].color = this.currentPosition.color;
        this.drawPosition(this.currentPosition);

        //move to next Box
        if(this.stateTransitions[currentStatus.color] === 'L') {
        // if (currentStatus.color === 0) {
            this.currentPosition.heading = (this.currentPosition.heading + 1)%4;
            // console.log('right');
        } else if (this.stateTransitions[currentStatus.color] === 'R') {
            this.currentPosition.heading = (this.currentPosition.heading + 3)%4;
            // console.log('left');
        }

        switch(this.currentPosition.heading) {
            case 0: this.currentPosition.y--;
                break;
            case 1: this.currentPosition.x++;
                break;
            case 2: this.currentPosition.y++;
                break;
            case 3: this.currentPosition.x--;
                break;
        }
        this.currentPosition.color = this.grid[this.currentPosition.x][this.currentPosition.y].color;

    }
    ...
    ... 
```

我们将致力于构建更通用的“特米特”，但现在让我们专注于我们新出生的蚂蚁，更具体地说是背景，我们的宇宙，画布。

> 进入:[canvasrenderingcontext 2d . globalcompositeoperation](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation)
> 这可以用来确定颜色的混合模式。
> 在加载 ctx 变量后的任何地方引入这一行

```
kC.ctx.globalCompositeOperation = '<your choice!>'; 
```

我最喜欢的:多重

*   将类型设置为乘，顶层的像素与底层的相应像素相乘。结果是一幅更黑暗的画面。
*   选择所有蚂蚁的颜色为相同的颜色。

我们看到道路越走越暗！

注意:我运行一个本地服务器，然后提供 js 文件。我用

```
http-server .\. --cors=* 
```

相同的源代码可以在这里找到: [Github](https://github.com/karx/kCanvas/)

## 项目状态截至 2019 年 8 月最后一次更新

我想看看这种逻辑在三维空间中的表现，所以用 [A-Frame](https://aframe.io/) 创建了同样的东西。

可以在这里查看 VR 中的兰顿蚂蚁:[VR 中的兰顿蚂蚁](https://kaaro.akriya.co.in/kCanvas/langton3d/kaaro.html)

在 4 个方向上，我们有

```
 new LangtonTermite(1,0,-10,5, ['L', 'L', 'R' , 'R'], 4),
    new LangtonTermite(30,0,20,1, ['L', 'L', 'R' , ], 3),
    new LangtonTermite(-40,0,20,1, ['L', 'R', 'R' , ], 3),
    new LangtonTermite(1,0,50,5, ['L', 'R' ], 2), 
```

这里每个蚂蚁分别取以下参数`start_x`、`start_y`、`start_z`、`orientation (default: 5)`、`transition (default: ['L', 'R'])`、`numberOfStates (default: 2)`、
源代码: [Github](https://github.com/karx/kCanvas/blob/master/langton3d/)

喜欢并分享帖子，有助于 movitate。