# 画布 101:旋转形状

> 原文：<https://dev.to/brianmontanaweb/canvas-101-rotating-shape-28la>

以前从没在画布上做过任何东西？！这对你来说太完美了！

让我们从设置和使用 Canvas API 的一些概念开始。我们可以把画布想象成一个可编程的蚀刻草图，你每次都可以在几分之一秒内擦除和绘制。

我们可以在 HTML、CSS 和 JavaScript 中设置前几行代码。我们将使用 CodePen，因为这是一种简单的方法，可以直接用 Canvas 开始构建。您将创建一个新的笔，将 CSS 设置为 normalize，将`<canvas id="canvas"></canvas>`添加到 HTML，并将`html { overflow: hidden; }`添加到 CSS。

现在我们可以开始用画布搭建，分解每一步。

```
const canvas = document.querySelector("#canvas");
const context = canvas.getContext("2d");
let canvasWidth = canvas.width = window.innerWidth;
let canvasHeight = canvas.height = window.innerHeight; 
```

我们将用`const canvas = document.querySelector("#canvas");`存储对 HTMLCanvasElement 的引用，这将让我们访问属性和方法来开始绘制。`const context = canvas.getContext("2d");`进入画布 HTMLCanvasElement 返回要绘制的画布的上下文。`canvasWidth`和`canvasHeight`正在使用窗口的属性将宽度和高度应用到画布上。

首先让我们构建`render`函数来设置`context`的颜色，然后绘制一个形状`fillRect()`作为背景。

```
function render(){
  context.fillStyle = 'lightsalmon';
  context.fillRect(0, 0, canvasWidth, canvasHeight);
} 
```

接下来我们可以为一个形状构造一个类，让我们把它做成一个正方形◻️，并在构造函数`{ width, height, rotate, xPosition, yPosition }`中赋予它这些属性。我们将在构造函数中解构传入的对象并设置默认值。在创建了 Square 类之后，让我们创建一个 Square 的实例并设置一些属性。

```
class Square {
  constructor({
    width,
    height,
    rotate = 0,
    xPosition = canvasWidth / 2,
    yPosition = canvasHeight / 2
  }) {
    this.width = width;
    this.height = height;
    this.rotate = rotate;
    this.xPosition = xPosition;
    this.yPosition = yPosition;
  }
}

const square = new Square({width: 50, height: 50}); 
```

现在我们已经创建了 Square 类及其实例。我们可以开始将它添加到 render 方法中。所以，让我们回到这个话题，做几个重要的步骤。`context.save()`方法将允许我们保存转换、特定属性、剪辑等。允许您在画布的绘图上下文中放置多个形状，并且`context.restore()`将从`context.save()`恢复状态。

```
function render() {
  context.fillStyle = "lightsalmon";
  context.fillRect(0, 0, canvasWidth, canvasHeight);
  context.save();
  // Add styling for square in the context here!
  context.restore();
} 
```

设置时不会有任何变化，但它将允许我们开始在评论部分构建形状和样式！所以让我们给下一个绘制上下文`context.fillStyle`添加一个更深的颜色，用`context.translate()`、`context.rotate()`设置上下文转换矩阵，然后用`context.fillRect()`在上下文中绘制。

```
function render() {
  context.fillStyle = "lightsalmon";
  context.fillRect(0, 0, canvasWidth, canvasHeight);
  // animation method
  context.save();
  context.fillStyle = "salmon";
  context.translate(square.xPosition, square.yPosition);
  context.rotate(square.rotate);
  context.fillRect(-square.width/2, -square.height/2, square.width, square.height);
  context.restore();
  // requestAnimationFrame
} 
```

厉害！你在画布上画了一个形状...现在让我们来制作动画吧！我们将创建一个移动方法来增加`square`的旋转和位置。变换矩阵属性`rotate`为 0 到 1 的值；1 代表 360 度。我们在哪里放置运动方法来操纵`square`属性将非常重要。

```
function movement(shape) {
  shape.rotate += 0.01;
  shape.xPosition += 0.1;
} 
```

既然我们有了移动方法，让我们从将其构建到 render 方法中开始。最重要的是，我们需要确保我们不会在每次移动方法出现时不断更新上下文转换矩阵。所以`context.save`确保这不会发生，并且`context.restore`再次应用初始状态。最后我们将使用 requestAnimationFrame 方法，所以我们每秒每 60 帧调用一次 render 方法:D

```
function render() {
  context.fillStyle = "lightsalmon";
  context.fillRect(0, 0, canvasWidth, canvasHeight);
  movement(square);
  context.save();
  context.fillStyle = "salmon";
  context.translate(square.xPosition, square.yPosition);
  context.rotate(square.rotate);
  context.fillRect(-square.width/2, -square.height/2, square.width, square.height);
  context.restore();
  window.requestAnimationFrame(render);
}

window.requestAnimationFrame(render); 
```

我们走吧！正方形在慢慢旋转，飞离画布！你可以在 CodePen :D 网站上查看最终版本

[https://codepen.io/brianmontanaweb/embed/jgWPmV?height=600&default-tab=result&embed-version=2](https://codepen.io/brianmontanaweb/embed/jgWPmV?height=600&default-tab=result&embed-version=2)