# 让我们编写一些快乐的文本。

> 原文：<https://dev.to/victorqribeiro/let-s-code-a-happy-text-5dm4>

# 快乐正文

让我们从规范 CSS 开始这个教程。

```
body {
  margin: 0;
  padding: 0;
}

canvas {
  display: block;
} 
```

这将摆脱侧栏时，工作与全尺寸的画布。

现在，让我们创建一个与页面大小相同的画布，并将其附加到页面主体。

```
const canvas = document.createElement('canvas');
const w = innerWidth,
  h = innerHeight,
  w2 = w / 2,
  h2 = h / 2;
canvas.width = w;
canvas.height = h;

document.body.appendChild(canvas); 
```

现在我们可以得到一个画布 2D 上下文，并定义一些文本以及一些文本参数。

```
const c = canvas.getContext('2d');
const fontsize = 30;
c.font = fontsize + "px Arial";

let text = "Happy Text!"; 
```

然后，我们创建一个函数来清空画布，并将文本绘制到屏幕的每一帧。

```
function draw() {

  /* clear the canvas from 0,0 to w,h - width and height of the page */
  c.clearRect(0, 0, w, h);

  /* draw the text on coords 10, 10 */
  c.fillText(text, 10, 10 );

  /* calls the draw function every frame */
  requestAnimationFrame(draw);
}

draw(); 
```

嗯，那都没问题，但是那条短信一点都不开心。

我们应该分别画出每个字母，然后制作动画，而不是仅仅画出文字。

我们可以用一个 for 循环，迭代文本中的每个字符，用一个正弦波，控制每个字母的 Y 位置。

所以，现在我们的 draw 函数看起来像这样:

```
/* angle that controls the sine wave */
let a = 0;

/* the size of the text, so we can centralize it on the screen */
const xoffset = c.measureText(text).width;

function draw() {
  c.clearRect(0, 0, w, h);
  /* for loop to iterate each char from text */
  for (let i = 0; i < text.length; i++) {
    c.fillText(text[i], 
      (w2 - xoffset) + (i * fontsize), /* centralized X position */
      h2 + (Math.sin(a - i) * 10)); /* sine wave offset by char position */
  }
  a += 0.08; /* increase the angle each frame, so the sine wave moves */
  requestAnimationFrame(draw);
}

draw(); 
```

就是这样。我们的快乐短信都做好了。

完整代码:

```
const canvas = document.createElement('canvas');
const w = innerWidth,
  h = innerHeight,
  w2 = w / 2,
  h2 = h / 2;
canvas.width = w;
canvas.height = h;

document.body.appendChild(canvas);

const c = canvas.getContext('2d');
const fontsize = 30;
c.font = fontsize + "px Arial";

let text = "Happy Text!";

let a = 0;

const xoffset = c.measureText(text).width;

function draw() {
  c.clearRect(0, 0, w, h);
  for (let i = 0; i < text.length; i++) {
    c.fillText(text[i],
      (w2 - xoffset) + (i * fontsize),
      h2 + (Math.sin(a - i) * 10));
  }
  a += 0.08;
  requestAnimationFrame(draw);
}

draw(); 
```

[用代码链接到 JSFiddle](http://jsfiddle.net/victorqribeiro/63e5s1pf/) 。