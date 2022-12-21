# 一个更真实的 HTML 画布绘画工具

> 原文：<https://dev.to/ascorbic/a-more-realistic-html-canvas-paint-tool-313b>

在 JavaScript 中创建一个基本的画布绘制工具是一项简单的工作，但结果却更像是 MS Paint 而不是 Monet。然而，通过一些改变，你可以制作出一个更加真实的工具。继续阅读，了解如何建立一个画布画笔，鬃毛由鬃毛。

让我们从最基本的实现开始。首先，您需要在页面中设置一个简单的 canvas 元素。

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        Drawing tools
        <style>
            body {
                margin: 0;
            }
            canvas {
                border: 2px solid black;
            }
        </style>
        <script src="src/index.js" defer></script>
    </head>
    <body>
        <canvas id="canvas" height="600" width="800"></canvas>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

基本程序是观察`mousedown`或`touchstart`事件，在这一点上你开始绘图。然后在`touchmove`或`mousemove`上，你从先前的笔刷位置到当前位置画一条线。您可以添加几个侦听器来处理绘图的结束。

下面是鼠标事件的基本绘图处理程序:

```
// Brush colour and size
const colour = "#3d34a5";
const strokeWidth = 25;

// Drawing state
let latestPoint;
let drawing = false;

// Set up our drawing context
const canvas = document.getElementById("canvas");
const context = canvas.getContext("2d");

// Drawing functions

const continueStroke = newPoint => {
    context.beginPath();
    context.moveTo(latestPoint[0], latestPoint[1]);
    context.strokeStyle = colour;
    context.lineWidth = strokeWidth;
    context.lineCap = "round";
    context.lineJoin = "round";
    context.lineTo(newPoint[0], newPoint[1]);
    context.stroke();

    latestPoint = newPoint;
};

// Event helpers

const startStroke = point => {
    drawing = true;
    latestPoint = point;
};

const BUTTON = 0b01;
const mouseButtonIsDown = buttons => (BUTTON & buttons) === BUTTON;

// Event handlers

const mouseMove = evt => {
    if (!drawing) {
        return;
    }
    continueStroke([evt.offsetX, evt.offsetY]);
};

const mouseDown = evt => {
    if (drawing) {
        return;
    }
    evt.preventDefault();
    canvas.addEventListener("mousemove", mouseMove, false);
    startStroke([evt.offsetX, evt.offsetY]);
};

const mouseEnter = evt => {
    if (!mouseButtonIsDown(evt.buttons) || drawing) {
        return;
    }
    mouseDown(evt);
};

const endStroke = evt => {
    if (!drawing) {
        return;
    }
    drawing = false;
    evt.currentTarget.removeEventListener("mousemove", mouseMove, false);
};

// Register event handlers

canvas.addEventListener("mousedown", mouseDown, false);
canvas.addEventListener("mouseup", endStroke, false);
canvas.addEventListener("mouseout", endStroke, false);
canvas.addEventListener("mouseenter", mouseEnter, false); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要添加一些额外的处理程序来处理触摸事件。

```
const getTouchPoint = evt => {
    if (!evt.currentTarget) {
        return [0, 0];
    }
    const rect = evt.currentTarget.getBoundingClientRect();
    const touch = evt.targetTouches[0];
    return [touch.clientX - rect.left, touch.clientY - rect.top];
};

const touchStart = evt => {
    if (drawing) {
        return;
    }
    evt.preventDefault();
    startStroke(getTouchPoint(evt));
};

const touchMove = evt => {
    if (!drawing) {
        return;
    }
    continueStroke(getTouchPoint(evt));
};

const touchEnd = evt => {
    drawing = false;
};

canvas.addEventListener("touchstart", touchStart, false);
canvas.addEventListener("touchend", touchEnd, false);
canvas.addEventListener("touchcancel", touchEnd, false);
canvas.addEventListener("touchmove", touchMove, false); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个工作示例。

[https://codesandbox.io/embed/4hzsj](https://codesandbox.io/embed/4hzsj)

可以换`strokeWidth`和`colour`，但是看起来不太像画笔。让我们开始解决这个问题。

这样做的第一个问题是它使用了一行代码。真正的画笔是由许多刚毛组成的。让我们看看是否可以通过添加刷毛来改善我们的刷子。

首先，我们将改变我们的笔画功能，绘制一个单一的刷毛，然后，当我们绘制一个笔刷笔画，我们将一次绘制几根刷毛。

```
const strokeBristle = (origin, destination, width) => {
    context.beginPath();
    context.moveTo(origin[0], origin[1]);
    context.strokeStyle = colour;
    context.lineWidth = width;
    context.lineCap = "round";
    context.lineJoin = "round";
    context.lineTo(destination[0], destination[1]);
    context.stroke();
};

const continueStroke = newPoint => {
    const bristleCount = Math.round(strokeWidth / 3);
    const gap = strokeWidth / bristleCount;
    for (let i = 0; i < bristleCount; i++) {
        strokeBristle(
            [latestPoint[0] + i * gap, latestPoint[1]],
            [newPoint[0] + i * gap, newPoint[1]],
            2
        );
    }
    latestPoint = newPoint;
}; 
```

Enter fullscreen mode Exit fullscreen mode

结果如下:

[https://codesandbox.io/embed/9y6xq](https://codesandbox.io/embed/9y6xq)

现在，这是一个改进，但它看起来更像一个梳子，而不是画笔。每根刷毛的宽度和位置都完全相同，这不太像真正的刷子。我们可以通过一些随机性来改善这一点。我们可以随机改变每根刷毛的宽度和位置，而不是以精确的间距来绘制刷毛。我们将在冲程开始时这样做，这样它在冲程长度内保持不变，但下次会改变。

首先，我们将创建一个助手函数来生成画笔，我们将把它存储为一个“刚毛”对象数组。

```
const makeBrush = size => {
    const brush = [];
    strokeWidth = size;
    let bristleCount = Math.round(size / 3);
    const gap = strokeWidth / bristleCount;
    for (let i = 0; i < bristleCount; i++) {
        const distance =
            i === 0 ? 0 : gap * i + Math.random() * gap / 2 - gap / 2;
        brush.push({
            distance,
            thickness: Math.random() * 2 + 2
        });
    }
    return brush;
};

let currentBrush = makeBrush(); 
```

Enter fullscreen mode Exit fullscreen mode

这使用了指定每个刷毛的宽度和位置的对象，然后我们可以使用这些对象来绘制笔画。

```
const strokeBristle = (origin, destination, width) => {
    context.beginPath();
    context.moveTo(origin[0], origin[1]);
    context.strokeStyle = colour;
    context.lineWidth = width;
    context.lineCap = "round";
    context.lineJoin = "round";
    context.lineTo(destination[0], destination[1]);
    context.stroke();
};

const drawStroke = (bristles, origin, destination) => {
    bristles.forEach(bristle => {
        context.beginPath();
        const bristleOrigin = origin[0] - strokeWidth / 2 + bristle.distance;

        const bristleDestination =
            destination[0] - strokeWidth / 2 + bristle.distance;
        strokeBristle(
            [bristleOrigin, origin[1]],
            [bristleDestination, destination[1]],
            bristle.thickness
        );
    });
};

const continueStroke = newPoint => {
    drawStroke(currentBrush, latestPoint, newPoint);
    latestPoint = newPoint;
};

const startStroke = point => {
    currentBrush = makeBrush(strokeWidth);
    drawing = true;
    latestPoint = point;
}; 
```

Enter fullscreen mode Exit fullscreen mode

结果如下:

[https://codesandbox.io/embed/imyr1](https://codesandbox.io/embed/imyr1)

这看起来好多了。刷毛已经看起来更自然了。然而，它看起来仍然比真正的笔刷更均匀。问题是颜色太单调了。一个真实的笔画会根据颜料的厚度和光线的角度而有轻微的颜色变化。我们可以像改变厚度和位置一样，通过稍微改变颜色来模拟这一点。为此，我们将使用一个名为 [TinyColor](https://github.com/bgrins/TinyColor) 的库。这个包的名字是 [`tinycolor2`](https://www.npmjs.com/package/tinycolor2) ，所以`npm install`它并包含在你的文件中，或者如果你不传输，你可以从 CDN 中包含它。

首先创建一个助手来随机改变颜色的亮度。

```
import tinycolor from "tinycolor2";

const varyBrightness = 5;

const varyColour = sourceColour => {
    const amount = Math.round(Math.random() * 2 * varyBrightness);
    const c = tinycolor(sourceColour);
    const varied =
        amount > varyBrightness
            ? c.brighten(amount - varyBrightness)
            : c.darken(amount);
    return varied.toHexString();
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以扩展`makeBrush`方法来添加一个`colour`属性。

```
const makeBrush = size => {
    const brush = [];
    let bristleCount = Math.round(size / 3);
    const gap = strokeWidth / bristleCount;
    for (let i = 0; i < bristleCount; i++) {
        const distance =
            i === 0 ? 0 : gap * i + Math.random() * gap / 2 - gap / 2;
        brush.push({
            distance,
            thickness: Math.random() * 2 + 2,
            colour: varyColour(colour)
        });
    }
    return brush;
}; 
```

Enter fullscreen mode Exit fullscreen mode

...然后修改绘图功能以使用刷毛颜色:

```
const strokeBristle = (origin, destination, bristle) => {
    context.beginPath();
    context.moveTo(origin[0], origin[1]);
    context.strokeStyle = bristle.colour;
    context.lineWidth = bristle.thickness;
    context.lineCap = "round";
    context.lineJoin = "round";
    context.lineTo(destination[0], destination[1]);
    context.stroke();
};

const drawStroke = (bristles, origin, destination) => {
    bristles.forEach(bristle => {
        context.beginPath();
        const bristleOrigin = origin[0] - strokeWidth / 2 + bristle.distance;

        const bristleDestination =
            destination[0] - strokeWidth / 2 + bristle.distance;
        strokeBristle(
            [bristleOrigin, origin[1]],
            [bristleDestination, destination[1]],
            bristle
        );
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

结果如下:

[https://codesandbox.io/embed/uj70w](https://codesandbox.io/embed/uj70w)

我现在对那些笔画的样子很满意，但现在的问题是动作。这里的笔刷有一个固定的角度，更像是一支记号笔。真正的画笔会随着你的移动而改变角度。为了做到这一点，我们可以使角度与我们移动的方向相匹配。这需要一些数学知识。

在我们的移动处理器中，我们知道前一个点和新点。由此我们可以算出方位，这给了我们画笔的新角度。然后我们为每根刷毛从它的旧位置和角度到它的新位置和角度画一条线。

首先，我们将添加一些帮助者，他们用三角学来计算这些角度。

```
const rotatePoint = (distance, angle, origin) => [
    origin[0] + distance * Math.cos(angle),
    origin[1] + distance * Math.sin(angle)
];

const getBearing = (origin, destination) =>
    (Math.atan2(destination[1] - origin[1], destination[0] - origin[0]) -
        Math.PI / 2) %
    (Math.PI * 2);

const getNewAngle = (origin, destination, oldAngle) => {
    const bearing = getBearing(origin, destination);
    return oldAngle - angleDiff(oldAngle, bearing);
};

const angleDiff = (angleA, angleB) => {
    const twoPi = Math.PI * 2;
    const diff =
        (angleA - (angleB > 0 ? angleB : angleB + twoPi) + Math.PI) % twoPi -
        Math.PI;
    return diff < -Math.PI ? diff + twoPi : diff;
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以更新我们的绘图函数来使用这些角度。

```
let currentAngle = 0;

const drawStroke = (bristles, origin, destination, oldAngle, newAngle) => {
    bristles.forEach(bristle => {
        context.beginPath();
        const bristleOrigin = rotatePoint(
            bristle.distance - strokeWidth / 2,
            oldAngle,
            origin
        );

        const bristleDestination = rotatePoint(
            bristle.distance - strokeWidth / 2,
            newAngle,
            destination
        );
        strokeBristle(bristleOrigin, bristleDestination, bristle);
    });
};

const continueStroke = newPoint => {
    const newAngle = getNewAngle(latestPoint, newPoint, currentAngle);
    drawStroke(currentBrush, latestPoint, newPoint, currentAngle, newAngle);
    currentAngle = newAngle % (Math.PI * 2);
    latestPoint = newPoint;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这给出了以下内容:

[https://codesandbox.io/embed/jd43r](https://codesandbox.io/embed/jd43r)

这是一个比以前更自然的动作，但转弯有点奇怪。这是因为它的角度发生了急剧变化。我们可以使用贝塞尔曲线来改善这一点。

首先，更新`drawStroke`来计算曲线的控制点。我们使用原点的位置，旋转到新的角度。

```
const drawStroke = (bristles, origin, destination, oldAngle, newAngle) => {
    bristles.forEach(bristle => {
        context.beginPath();
        const start = bristle.distance - strokeWidth / 2;

        const bristleOrigin = rotatePoint(start, oldAngle, origin);
        const bristleDestination = rotatePoint(start, newAngle, destination);

        const controlPoint = rotatePoint(start, newAngle, origin);

        strokeBristle(bristleOrigin, bristleDestination, bristle, controlPoint);
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们更新`strokeBristle`来使用曲线而不是直线:

```
const strokeBristle = (origin, destination, bristle, controlPoint) => {
    context.beginPath();
    context.moveTo(origin[0], origin[1]);
    context.strokeStyle = bristle.colour;
    context.lineWidth = bristle.thickness;
    context.lineCap = "round";
    context.lineJoin = "round";
    context.shadowColor = bristle.colour;
    context.shadowBlur = bristle.thickness / 2;
    context.quadraticCurveTo(
        controlPoint[0],
        controlPoint[1],
        destination[0],
        destination[1]
    );
    context.stroke();
}; 
```

Enter fullscreen mode Exit fullscreen mode

这非常有效，除了当我们第一次开始一个笔画时，它试图从之前笔刷的角度弯曲，这产生了一些不自然的结果。我们最后的改变将是在开始划水时不使用曲线。

```
let currentAngle;

const getNewAngle = (origin, destination, oldAngle) => {
    const bearing = getBearing(origin, destination);
    if (typeof oldAngle === "undefined") {
        return bearing;
    }
    return oldAngle - angleDiff(oldAngle, bearing);
};

// ...

const startStroke = point => {
    currentAngle = undefined;
    currentBrush = makeBrush(strokeWidth);
    drawing = true;
    latestPoint = point;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是最终版本:

[https://codesandbox.io/embed/uwmot](https://codesandbox.io/embed/uwmot)

现在，虽然我喜欢紫色，你可能想用一些其他的颜色。这是一个简单的加法，带有很少使用的`<input type="color">` :

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, user-scalable=no" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    Drawing tools
    <style>
      body {
        margin: 0;
      }
      canvas {
        border: 2px solid black;
      }

      #colourInput {
        position: absolute;
        top: 10px;
        left: 10px;
      }
    </style>
    <script src="src/index.js" defer></script>
  </head>
  <body>
      <canvas id="canvas" height="450" width="800"></canvas>
      <input type="color" id="colourInput" value="#3d34a5" />
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

然后在开始每个冲程时读取该值:

```
const startStroke = point => {
    colour = document.getElementById("colourInput").value;
    currentAngle = undefined;
    currentBrush = makeBrush(strokeWidth);
    drawing = true;
    latestPoint = point;
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可以用笔刷大小做类似的事情。你也可以尝试像笔刷预设这样的东西，改变刷毛的大小和数量。

这是包含颜色选择器的最终版本:

[https://codesandbox.io/embed/g362x](https://codesandbox.io/embed/g362x)

试试[全屏版](https://canvas-paint-tool.netlify.com/)。如果你有一些建议，在[的 GitHub 回购](https://github.com/ascorbic/canvas-paint-tool/)上开一个 PR