# #CodepenChallenge 100 点:跟随鼠标指针

> 原文：<https://dev.to/takaneichinose/codepenchallenge-100-dots-follow-the-mouse-pointer-47df>

这些点跟随你的鼠标指针。

应该是，我想让它有点恐怖。就像许多眼睛在观察你的一举一动。但是当我在做的时候，我发现这个动作太可爱了，就像五彩纸屑一样，所以我把它弄得五彩缤纷。

我没有使用文本点()，而是将其更改为边框半径为 50%的 span。毕竟更容易操控。

我没有使用任何库，也没有使用预处理器。

* * *

# 我是怎么做到的。

首先，你得回到高中，记住你的三角学课。我不会掩盖 HTML 和 CSS 代码，因为我只使用了一个简单的边框半径，我相信每个人都可以舒适地使用。

### 定义常数

首先，我们必须得到每个圆的容器的 x 和 y 位置。

```
// The circle
const p = i.querySelector('span');
// Getting the position of container of the circle
const q = i.getBoundingClientRect();
// 'x' position of container
const x1 = q.left;
// 'y' position of container
const y1 = q.top; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们必须得到每个圆及其容器的大小，以测量边界。

```
// Width of container
const iw = i.offsetWidth / 2;
// Height of container
const ih = i.offsetHeight / 2;
// Width of circle
const pw = p.offsetWidth / 2;
// Height of circle
const ph = p.offsetHeight / 2; 
```

Enter fullscreen mode Exit fullscreen mode

最后，鼠标的位置

```
'x' position of the mouse
const x2 = e.clientX;
'y' position of the mouse
const y2 = e.clientY; 
```

Enter fullscreen mode Exit fullscreen mode

### 计算资源

现在，我们已经为运动定义了所有需要的常数，让我们为运动做一点计算。

为了让我们的圆跟随鼠标的位置，我们需要得到从中心位置到鼠标位置的角度。我们必须使用勾股定理。

幸运的是，因为 Javascript 将完成大部分工作，我们只需减去圆心的“x”位置和鼠标的“x”位置。还有，圆心的‘y’位置，鼠标位置的‘y’位置。

然后，使用 Javascript 已经提供的反正切平方函数。

根据定律，cos()代表“x”轴，sin()代表“y”轴(是的，我认为它是这样工作的)。

```
let px = x2 - x1;
let py = y2 - y1;
let t  = Math.atan2(py, px);
let ax = iw + (pw * Math.cos(t));
let ay = ih + (ph * Math.sin(t)); 
```

Enter fullscreen mode Exit fullscreen mode

如果圆圈从边界出来会很奇怪，对吗？所以我们必须为每个圆做碰撞，这样它就不会从它的边界出来。

```
// Yeah, this is just a simple collision algorithm but it really does its job. Even thou it is just for square, I just taken an advantage of how the shapes looks, so it is quite fine.
if (x2 > x1 && y2 > y1) {
  px = (px + iw > ax) ? (ax - iw) : px;
  py = (py + ih > ay) ? (ay - ih) : py;
}
else if (x2 < x1 && y2 < y1) {
  px = (px + iw < ax) ? (ax - iw) : px;
  py = (py + ih < ay) ? (ay - ih) : py;
}
else if (x2 > x1 && y2 < y1) {
  px = (px + iw > ax) ? (ax - iw) : px;
  py = (py + ih < ay) ? (ay - ih) : py;
}
else if (x2 < x1 && y2 > y1) {
  px = (px + iw < ax) ? (ax - iw) : px;
  py = (py + ih > ay) ? (ay - ih) : py;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，根据位置渲染圆。

```
p.style.left = (px + pw) + 'px';
p.style.top  = (py + pw) + 'px'; 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

我听到许多人说我们在编程中不再需要数学了。我认为他们错了。因为在我看来，如果你不知道这些东西是如何工作的，你似乎不太知道如何使用每种编程语言提供的功能。

仍然有很多东西需要学习，使用我们数学课上教授的原理。几何比较容易，但是统计学。我们需要它，例如机器学习、深度学习、人工智能以及任何相关的原理。对于游戏开发，我们需要学习物理，即使以我们目前的技术，它也还不完善。

感谢您的阅读！如果您有意见、建议或反应，请在下面评论。

* * *

我在寻找更好，更平滑，更快的算法。如果你能提供给我，请在下面评论。

* * *

资源:
[在那里我读到了勾股定理](https://en.wikipedia.org/wiki/Pythagorean_theorem)
[在那里我读到了 Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

[https://codepen.io/takaneichinose/embed/PoYGEmd?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/PoYGEmd?height=600&default-tab=result&embed-version=2)