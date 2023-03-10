# 如何在 DEV 的离线页面上创建绘图交互

> 原文：<https://dev.to/aspittel/how-to-create-the-drawing-interaction-on-dev-s-offline-page-1mbe>

由于越来越多的人[已经注意到](https://www.reddit.com/r/webdev/comments/c8a35w/devto_error_page_start_drawing) DEV 的[离线页面](https://dev.to/offline)，我想我应该做一个快速的教程，教你如何复制它的代码！

Canvas 是用来用 JavaScript 创建图形的——我们可以用它来构建有趣的交互式工具。当我通常构建这样的交互式艺术品时，我使用 [P5.js](https://p5js.org/) ，这使得 Canvas API 更容易使用。不过，我们想让离线页面尽可能的独立和轻量级，所以离线页面不使用任何外部代码。

我们需要做的第一件事是在 HTML 中创建一个`<canvas>`标签。你还需要添加 CSS 来使`canvas`占据空间——所以给它一个高度和宽度。我用一些初学者 CSS 制作了一个 Codepen 模板，供我们使用:

[https://codepen.io/aspittel/embed/vzrgGY?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/vzrgGY?height=600&default-tab=result&embed-version=2)

现在，来看看 JavaScript 吧！

我们需要做的第一件事是选择 HTML 中已经存在的 canvas 元素，这样我们就可以与它进行交互。我们还必须为画布的上下文创建一个变量。我们将使用 2D 上下文，因为我们的绘图将只是二维的

```
const canvas = document.querySelector('canvas')
const context = canvas.getContext('2d') 
```

我们还想在 JavaScript 中设置画布的大小，这样我们的图像就不会扭曲:

```
canvas.setAttribute('width', window.innerWidth)
canvas.setAttribute('height', window.innerHeight) 
```

现在我们需要添加一些事件监听器。对于绘图应用程序，我们希望添加以下内容:

*   当用户按下鼠标时，我们想要开始绘图

*   “触摸开始”——当用户在打电话时，我们又想开始画画

*   当用户移动鼠标时，我们希望从鼠标的前一个位置到当前位置画一条线

*   “触摸移动”——同上，但当用户在打电话时

*   当用户停止按下鼠标时，我们希望停止绘图

*   当用户的鼠标离开该区域时，我们也想停止绘图

*   “触摸结束”——当用户在打电话并停止按下时，我们再次想要停止绘画

因此，我们需要三个事件处理函数来响应上述事件。让我们从`startPaint`函数开始，它将在用户每次开始绘画时运行。

我们可以像在 JavaScript 中添加任何其他元素一样添加事件监听器:

```
 function startPaint (e) {

}

canvas.addEventListener('mousedown', startPaint)
canvas.addEventListener('touchstart', startPaint) 
```

我们希望`startPaint`函数做几件事:

*   首先，我们需要一个变量来跟踪我们当前是否正在绘制，以便`mousemove`处理程序只在我们当前正在绘制时工作。每当我们开始绘图时，我们需要将它设置为`true`。

*   然后，我们需要得到这个人点击的坐标。我们需要跟踪这些坐标，以便当人们移动鼠标时，我们可以从当前点移动到下一个点。

```
 let x, y, isPainting;

function getCoordinates(event) {
  // check to see if mobile or desktop
  if (["mousedown", "mousemove"].includes(event.type)) {
    // click events
    return [event.pageX - canvas.offsetLeft, event.pageY - canvas.offsetTop];
  } else {
    // touch coordinates
    return [
      event.touches[0].pageX - canvas.offsetLeft,
      event.touches[0].pageY - canvas.offsetTop
    ];
  }
}

function startPaint(e) {
  // change the old coordinates to the new ones*
  isPainting = true;
  let coordinates = getCoordinates(e);
  x = coordinates[0];
  y = coordinates[1];
} 
```

[https://codepen.io/aspittel/embed/JaZWvE?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/JaZWvE?height=600&default-tab=result&embed-version=2)

然后，我们需要处理什么时候人移动他们的鼠标来画画。在这里我们必须:

*   查看我们是否正在绘画(即鼠标按下)

*   我们需要得到新的鼠标坐标

*   我们需要在旧坐标和新坐标之间画一条线

*   我们需要将旧坐标设置为新坐标，这样我们的下一次“绘制”就从当前点
    开始

```
function drawLine(firstX, firstY, secondX, secondY) {
  // set the attributes of the line
  context.strokeStyle = "black";
  context.lineJoin = "round";
  context.lineWidth = 5;

  context.beginPath();
  context.moveTo(secondX, secondY);
  context.lineTo(firstX, firstY);
  context.closePath();

  // actually draw the path*
  context.stroke();
}

function paint(e) {
  if (isPainting) {
    let [newX, newY] = getCoordinates(e);
    drawLine(x, y, newX, newY);

    // Set x and y to our new coordinates
    x = newX;
    y = newY;
  }
}

canvas.addEventListener("mousemove", paint);
canvas.addEventListener("touchmove", paint); 
```

现在，我们只需在松开鼠标时停止绘制！

```
 function exit() {
  isPainting = false;
}

canvas.addEventListener("mouseup", exit);
canvas.addEventListener("mouseleave", exit);
canvas.addEventListener("touchend", exit); 
```

[https://codepen.io/aspittel/embed/QVxpoX?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/QVxpoX?height=600&default-tab=result&embed-version=2)

现在，这是一个完成的版本，可以改变颜色和调整页面大小！

[https://codepen.io/aspittel/embed/ZMRKwX?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/ZMRKwX?height=600&default-tab=result&embed-version=2)

我喜欢用代码构建艺术，尤其是人们可以与之互动的艺术。如果你想了解更多，如果你感兴趣的话，我还有一些关于这个话题的帖子！

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 生成艺术导论

### 阿里·斯皮特尔 9 月 25 日 186 分钟阅读

#art #frontend #javascript #showdev](/aspittel/intro-to-generative-art-2hi7)[![video camera](img/6ce423bda928ca22c2d375147e9ee787.png) 1:18:08 ](/aspittel/learning-css-through-creating-art-54c0) [![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 通过艺术创作学习 CSS

### 阿里·斯皮特尔 6 月 27 日 181 分钟阅读

#devlive #webdev #css](/aspittel/learning-css-through-creating-art-54c0)