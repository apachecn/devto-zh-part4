# 画布介绍

> 原文：<https://dev.to/mishafb/an-introduction-to-the-canvas-2ffe>

## 先决条件:

本教程是为初学者编写的。如果你知道`let`是块作用域`var`并且知道如何使用`const`就足够了。

### 这是给谁的

到目前为止，您的大多数 web 应用程序可能包括从元素中获取输入、监听按钮、修改文本，甚至可能创建新元素。这个快速教程将教你如何从基本形状开始用 JS 制作图形，但是可能性是无穷的！

## <画布>元素

canvas 元素(从现在开始称为 canvas)是唯一可以在上面绘制的元素。在画布上画画之前，它是完全透明的。画布的默认大小是 300 x 150 像素。这个尺寸可以用`width`和`height`属性来改变。

**注意:**可以用 CSS 缩放画布，但是如果长宽比(宽高比)不同，图像会拉伸。

**注意:**截至本文撰写时， [98.9%的浏览器支持 canvas](https://caniuse.com/#feat=canvas) ，所以你不应该担心兼容性，因为这和 [CSS3 框定大小](https://caniuse.com/#feat=css3-boxsizing)一样常见。

## 设置好一切

要在画布上绘图，首先在 JS 中获取对画布的引用。最简单也是最常见的方法是使用 [`document.getElementById('id')`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) ，它返回具有特定`id`属性的元素。

index.html

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    Canvas Tutorial
    <link href="style.css" rel="stylesheet" type="text/css" />
  </head>
  <body>
    <canvas id="my-first-canvas" width="480" height="270"></canvas>
    <script src="script.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

**注意:** <画布>标签必须关闭。

style.css

```
body {
  margin: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

script.js

```
const canvas = document.getElementById('my-first-canvas') 
```

Enter fullscreen mode Exit fullscreen mode

如果你想省去自己复制粘贴这个的麻烦，你可以[在 Repl.it](https://repl.it/@liltaco/Canvas-Tutorial-Starter) 上叉这个超级简约的入门。以下所有片段仅适用于 JS；HTML 和 CSS 将保持不变。

## 渲染上下文

画布元素只是一个元素。为了在上面画画，你需要得到一个*渲染上下文*。渲染上下文是您可以在画布上绘制的方式。目前，这些是 [`CanvasRenderingContext2D`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D) 和 [`WebGLRenderingContext`](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext) 。2D 是最容易共事的人；它给你各种形状、文本和图像的功能。2D 渲染上下文的主要缺点是它运行在 CPU 上，而不是 GPU 上，所以它比 WebGL 慢得多。WebGL 是 OpenGL ES 2.0(一个低级图形库)到 web 的一个端口，允许在 GPU 上使用高级图形。但是，没有库使用起来非常复杂。本教程将只使用 2D 渲染环境。

要获得 2D 渲染上下文，只需输入:

```
const ctx = canvas.getContext('2d') 
```

Enter fullscreen mode Exit fullscreen mode

## 绘图

现在您已经有了渲染上下文，您可以绘制您的第一个矩形:

```
ctx.fillRect(0, 10, 50, 100) 
```

Enter fullscreen mode Exit fullscreen mode

`ctx.fillRect`接受 4 个参数:`x, y, width, height`。线条`ctx.fillRect(0, 0, 50, 100)`将填充一个宽 50 高 100 的矩形，其左上角在`x = 0`和`y = 10`。

位置`x: 0, y: 0`在左上角，所以较高的 X 值向右，较高的 Y 值向下。

**注意:**你在画布上画的任何东西都会保留下来，直到你在它上面画了什么或者改变了`width`和`height`属性。

### 颜色

2D 渲染上下文中的颜色可以是任何 CSS 颜色，所以可以写成十六进制、`rgb(1, 2, 3)`、`hsl(120, 100%, 50%)`、`rgba`、`hsla`，方便的是可以使用一个[颜色关键字](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#Color_keywords)。

现在，让我们应用颜色的矩形。

有 [`ctx.fillStyle`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillStyle) 是填充形状的颜色， [`ctx.strokeStyle`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillStyle) 是轮廓形状的颜色。一旦您设置了颜色，您绘制的所有内容都将以该颜色绘制，直到您更改它。

```
ctx.fillStyle = 'red'
ctx.fillRect(0, 10, 50, 100)

ctx.strokeStyle = 'blue'
ctx.strokeRect(10, 20, 50, 75) // x, y, width, height 
```

Enter fullscreen mode Exit fullscreen mode

[![Result of the previous code snippet](img/665defcb24552e7750020a031447e537.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZTQML3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i0yb50xollw0b7xdhupr.png)

创作抽象艺术从未如此简单！

除了`fillRect`和`strokeRect`，还有 [`clearRect`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/clearRect) 。clearRect 也获得`x, y, width, height`参数，但是 clearRect 将使矩形内部的所有东西都透明。如果你想清除整个画布，你也可以做`canvas.width = canvas.width`或`canvas.height = canvas.height`，因为设置画布大小也会清除它。

## 高级形状

路径是一系列直线，可以是直线也可以是曲线。一旦你创建了一条路径，你就调用 [`ctx.fill()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fill) 或者 [`ctx.stroke()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/stroke) 或者甚至两者来在画布上绘制路径。

### 基本功能:

*   [`ctx.beginPath()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/beginPath) 重置路径，总是在画东西之前运行它，这样它就不会和你刚刚画的东西混淆了。
*   [`ctx.moveTo(x, y)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/moveTo)‘举起’路径笔并将其移动到一个位置。
*   [`ctx.lineTo(x, y)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/lineTo) 将路径笔移动到直线上的给定点。
*   [`ctx.closePath()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/closePath) 将路径笔从最后一点直线移动到第一点。

如果你想画曲线或者做一些更高级的事情比如路径裁剪，你可以从 MDN 上看到[完整的路径方法列表](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D#Paths)。

现在，让我们画我们的第一个三角形！

```
ctx.fillStyle = 'red'
ctx.fillRect(0, 10, 50, 100)

ctx.strokeStyle = 'blue'
ctx.strokeRect(10, 20, 50, 75)

ctx.beginPath()    // reset the path
ctx.moveTo(60, 20) // raise the pen to x = 60 and y = 20
ctx.lineTo(20, 50) // move the pen in a straight line to x = 20 and y = 50
ctx.lineTo(60, 80) // move the pen in a straight line to x = 60 and y = 80
ctx.closePath()    // move the pen back to the starting position of x = 60 and y = 20

// Note: when using ctx.fill(), ctx.closePath() is not required;
// if the path wasn't a closed one, ctx.fill() will draw it the same.
// However, ctx.stroke() will not.

ctx.fillStyle = 'green'
ctx.fill()
ctx.strokeStyle = 'blue'
ctx.lineWidth = 3
// ctx.lineWidth will decide how thick the outline is when running ctx.stroke()
ctx.stroke() 
```

Enter fullscreen mode Exit fullscreen mode

[![Result of the previous code snippet](img/c31976c14d7cc409d56ea5ed7e027213.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lME0qds8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0wkzglh7oeil8vvef7t3.png) 
它一起来了！

## 常见的形状

### 圆圈

没有`ctx.circle`函数，但是有 2 种主要的方法在画布上画圆。

1.  [`ctx.ellipse(x, y, radiusX, radiusY, rotation, startAngle, endAngle)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/ellipse)——在我写这篇文章的时候，Android webview 还不支持它，这是个问题。这就是为什么我通常使用:
2.  [`ctx.arc(x, y, radius, 0, Math.PI * 2)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/arc)——0 和数学。PI * 2 是起始角度和终止角度。

这里有一些你可以玩的圈子:

[https://repl.it/@liltaco/Canvas-Tutorial-Circles?lite=true](https://repl.it/@liltaco/Canvas-Tutorial-Circles?lite=true)

### 圆角矩形

没有`ctx.roundedRect()`函数，但是您可以使用来自 MDN:
的这个修改过的代码片段

```
CanvasRenderingContext2D.prototype.roundedRect = function (x, y, width, height, radius) {
  this.moveTo(x, y + radius);
  this.lineTo(x, y + height - radius);
  this.arcTo(x, y + height, x + radius, y + height, radius);
  this.lineTo(x + width - radius, y + height);
  this.arcTo(x + width, y + height, x + width, y + height-radius, radius);
  this.lineTo(x + width, y + radius);
  this.arcTo(x + width, y, x + width - radius, y, radius);
  this.lineTo(x + radius, y);
  this.arcTo(x, y, x, y + radius, radius);
} 
```

Enter fullscreen mode Exit fullscreen mode

只需将它添加到代码的开头，每个 2D 渲染上下文都将拥有`ctx.roundedRect`方法。( [`Object.prototype`](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes) 基本上是给每个实例一个新的方法)。

## 变换

有时，您可能想要缩放、移动或旋转您在画布上绘制的所有内容。

*   [`ctx.save()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/save) 推当前转换状态
*   [`ctx.restore()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/restore) 弹出先前的变换状态
*   [`ctx.translate(x, y)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/translate) 向右移动画布原点`x`单位，向下移动`y`单位。你画的所有东西都会被移动那么多。
*   [`ctx.scale(x, y)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/scale) 将每个单位乘以`x``y`；如果小于 1，它将缩小所有内容，如果大于 1，它将放大所有内容。
*   [`ctx.rotate(angle)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/rotate) 从现在开始按`angle`弧度旋转你画的一切。

### 转换顺序至关重要！

如果你先做`ctx.scale(2, 2)`，然后再做`ctx.translate(10, 10)`，那么所有的东西都会按照原来的比例平移 20 个单位，但是如果你先做`ctx.translate(10, 10)`，然后再做`ctx.scale(2, 2)`，所有的东西都会按照原来的比例平移 10 个单位。这同样适用于旋转。

### 变换叠加！

如果您运行`ctx.scale(1.1, 1.1)`，那么`ctx.scale(1.1, 1.1)`将再次将所有内容放大 21%。每个转换都将在先前的转换状态上叠加，就像在空转换状态上叠加一样。

试试我的[转型操场](https://canvas-tutorial-transformations-playground.liltaco.repl.co/)边做边学。

## 期末笔记

你通常不能把元素放在画布中，因为它们没有显示，但是如果用户有一个像 2009 年的 Internet Explorer 8 这样的旧浏览器，画布中的任何元素都是可见的。因此，你可以在画布上放置一些描述内容，或者说“你的浏览器不支持画布”作为后备。

如果你想在另一个元素上画画，只要用 CSS 把画布放在它上面，然后在画布上画画(记住画布默认是透明的)。

另一个有用的提示是，如果你想分层绘制，即在擦除覆盖图时不擦除背景(对于背景大部分是静态但需要绘制的游戏很有用)，你可以用 CSS 将一个画布放在另一个画布上。

## 本教程到此为止！

以下是您应该通读的一些要点:

*   [关于绘图文本的 MDN 教程](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Drawing_text#Drawing_text)
*   [MDN 图像绘制教程](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Using_images)

## **接下来:**鼠标和键盘为你的交互式 webapps 输入