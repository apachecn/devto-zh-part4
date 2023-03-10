# 形式和功能

> 原文：<https://dev.to/rowan_m/form-and-function-emm>

这是我如何建造 [系列文章的第二篇🥑 *Rotavo PWA*](https://bit.ly/rotavo-pwa) *。试一试，看看你能画出什么！*

在[的上一期](https://dev.to/rowan_m/you-spin-me-right-r-und-k32)中，我们构建了触摸控制的`<input-knob>`组件，让我们创建一个带有与其角度对应的`value`属性的旋钮。然而，[乐趣](https://paul-spinlan.glitch.me)尽管如此，它仍然缺少一些实用的功能。

## 目录

*   [♻️梳洗完毕](#freshen-up)
*   [👂相当大的事件](#quite-the-event)
*   [🔒强制限制](#enforcing-limits)
*   计数旋转
*   [🎁奖励内容](#bonus-content)

## ♻️梳洗一番

不过，首先，让我们用一些外观上的改进来刷新之前的演示。我说我们要做一个类似于立体声音量控制旋钮的东西。现在，我们有了旋转行为，但是我们的旋钮是方形的...传统上它们是圆的。你们都很友好，没有提到这一点，但我们应该在继续前进之前解决这个问题。

我们还需要一个指示器来指示旋钮上的“向上”方向。正如我们上次在[最终演示](https://paul-spinlan.glitch.me)中看到的，我们可以在元素中添加任何我们需要的内容。所以我们会在那里放一个小三角指示器:

```
<input-knob value="2.5"><div class="mark">▲</div></input-knob> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要设计它的样式，使它位于中心:

```
.mark {
  display: inline-block;
  width: 100%;
  text-align: center;
  font: bold 200% monospace;
  color: #356211;
} 
```

Enter fullscreen mode Exit fullscreen mode

控件的主要变化是使用`border-radius`来圆角。我们还将把阴影放在外部的`input-knob`元素上，因为它不会旋转。如果我们在内部`::part(container)`上有阴影，看起来好像阴影在元素周围移动，这不是我们想要的效果。

```
input-knob {
  border-radius: 100%;
  box-shadow: 0 0.3rem 0.3rem rgba(0, 0, 0, 0.5);
}

input-knob::part(container) {
  box-sizing: border-box;
  background: #cadbbc;
  border: 1rem double #356211;
  border-bottom: 1rem solid #356211;
  border-radius: 100%;
  width: 8rem;
  height: 8rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

圆角！最初的 CSS 圣杯-陶醉于其中。

[https://glitch.com/embed/#!/embed/building-rotavo-04?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-04?previewSize=100&path=index.html)

我们还可以对影子 DOM 模板上的`:host`的样式做一点改进。具体来说，我们希望光标变成小的👆向用户指示他们可以与之交互的指针。

```
:host {
  display: inline-block;
  user-select: none;
  touch-action: none;
  cursor: pointer;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 👂相当大的事件

现在我们给我们的元素添加了一些修饰，我们得到了一些视觉上令人愉悦的东西，但是当用户与元素交互时，很难对任何变化做出反应。就像我们监听元素内部的指针事件一样，我们想要发出我们自己的事件，以便在更广泛的应用程序中响应它们。

与指针事件非常相似，我们希望跟踪交互的开始、中间和结束。这意味着我们将[创建三种`Event`](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events) 类型:

*   `knob-move-start`:触摸/点击元素时
*   `knob-move-change`:移动元素时
*   `knob-move-end`:释放元素时

我们将在元素内部的每个处理程序的末尾发出这些事件，因为我们希望确保在任何试图处理事件的操作之前，我们已经在元素内部完成了所有必要的工作。

```
// class InputKnob
_rotationStart() {
  // ✂️ existing code hidden 
  const evt = new Event('knob-move-start', { bubbles: true });
  this.dispatchEvent(evt);
}

_rotationChange() {
  // ✂️ existing code hidden 
  const evt = new Event('knob-move-change', { bubbles: true });
  this.dispatchEvent(evt);
}

_rotationEnd() {
  // ✂️ existing code hidden
  const evt = new Event('knob-move-end', { bubbles: true });
  this.dispatchEvent(evt);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们需要确保我们指定了`bubbles: true`，因为我们的[侦听器将成为父元素](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#Event_bubbling_and_capture)。您可以尝试删除它，您将看到事件永远不会“冒泡”到父节点。

随着这些事件的触发，我们可以像监听其他事件一样监听它们:

```
document.addEventListener('knob-move-start', logEvent); 
```

Enter fullscreen mode Exit fullscreen mode

看一下下面的演示，看看我们如何使用`logEvent()`函数在事件触发时照亮一些`<span>`元素。

[https://glitch.com/embed/#!/embed/building-rotavo-05?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-05?previewSize=100&path=index.html)

## ⚖️有一些分寸感

目前，元素的`value`直接映射到它的角度。如果我们有一个从 0 到 11 的音量控制，那么这就是我们想要匹配的值。否则，我们就在强迫我们的开发人员自己进行从角度到值的转换，这是非常不礼貌的。为了解决这个问题，我们将添加一个`scale`属性，开发人员可以在其中指定完整旋转的值。

首先，让我们将新属性添加到元素中。我们想要通常的属性到属性的镜像，但是有一点需要注意——我们的缺省值是`1`,因为`scale`将是一个乘数，乘以`0`将总是给我们...嗯，又是`0`。让我们把它放在:

```
// class InputKnob
static get observedAttributes() {
  return ['value', 'scale'];
}

get scale() {
  return this.hasAttribute('scale') ? this.getAttribute('scale') :1;
}

set scale(scale) {
  this.setAttribute('scale', scale);
} 
```

Enter fullscreen mode Exit fullscreen mode

然而现在`value`和`_angle`依赖于`scale`，所以我们有一点连接要做。每当我们的一个属性改变时，我们需要确保我们重新计算:

```
attributeChangedCallback(attrName, oldVal, newVal) {
  this._angle = (TWO_PI / this.scale) * (this.value % this.scale);
  this._drawState();
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，如果我们的`scale`是 10，我们的`value`是 5，那么这应该是旋钮上的半圈——或者π的`_angle`——或者指向正下方。

匹配的部分现在是当`_angle`改变时，我们也需要更新`value`。

```
// _rotationChange()
this.value = this._angle / (TWO_PI / this.scale); 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果角度为π，那么我们应该期待值为 5。那是...嗯，这实际上是增加规模。因此，您可以在下面的演示中验证这一点。我们将等级设为 10，因此⬆️ = 0，➡️ = 2.5，⬇️ = 5，⬅️ = 7.5。给它一个🔃下面！

[https://glitch.com/embed/#!/embed/building-rotavo-06?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-06?previewSize=100&path=index.html)

作为一个小奖励，看看这个演示中的 CSS。布局使用了带有`grid-template-areas`的 CSS 网格布局，在这里你基本上绘制了一个你想要的布局的小文本图。所以，上面各项的排列就是字面上的:

```
grid-template-areas: 
  ".  ⬆️ . "
  "⬅️ 🎛️ ➡️"
  ".  ⬇️ . "; 
```

Enter fullscreen mode Exit fullscreen mode

我会在生产中推荐这个吗？谁知道呢...我是说，我见过更糟的。

## 🔒强制限制

虽然能够无限旋转旋钮有一定的异想天开的自由，但我们经常需要设置一些限制。如果音量控制允许低于 0 的值，那么它就没有意义，如果音量可以高于 11，谁知道会有什么后果。

让我们设置一些属性来保存元素的最小和最大限制，适当地命名为`min`和`max`。这与我们之前添加的`scale`属性是一样的，希望不会让人感到意外。

```
// class InputKnob
static get observedAttributes() {
  return ['value', 'scale', 'min', 'max'];
}

get min() {
  return this.hasAttribute('min') ? this.getAttribute('min') : null;
}

set min(min) {
  this.setAttribute('min', parseFloat(min));
}

get max() {
  return this.hasAttribute('max') ? this.getAttribute('max') : null;
}

set max(max) {
  this.setAttribute('max', parseFloat(max));
} 
```

Enter fullscreen mode Exit fullscreen mode

默认值是`null`,因为如果没有设置限制，我们不想强制执行。换句话说，如果属性是`null` : [![the limit does not exist](img/7b689897f8791fed325e1ecdb58ba5c4.png "The limit does not exist.")](https://i.giphy.com/media/7JvlHfd7C2GDr7zfZF/giphy.gif)

这意味着我们需要首先检查它们是否在界限内，而不是仅仅计算和设置`_angle`和`value`。计算保持不变，我们只是重命名为`_attemptedAngle`和`_attemptedValue`。然后，我们检查是否设置了极限，在我们传递值之前，我们尝试的值在它的右边。

```
// _rotationChange()
this._attemptedAngle =
  this._initialAngle
  - this._initialTouchAngle
  + Math.atan2(this._touchY - this._centerY, this._touchX - this._centerX);
this._attemptedAngle = (this._attemptedAngle + TWO_PI) % TWO_PI;
this._attemptedValue = this._attemptedAngle / (TWO_PI / this.scale);

if (
  (this.min === null || this._attemptedValue >= this.min) &&
  (this.max === null || this._attemptedValue <= this.max)
) {
  this._angle = this._attemptedAngle;
  this.value = this._attemptedValue;
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个逻辑，现在我们可以添加一个旋钮来限制它在两个值之间的移动:

```
<input-knob value="5" scale="10" min="2.5" max="7.5"> 
```

Enter fullscreen mode Exit fullscreen mode

在演示中尝试一下。想怎么转就怎么转，但是那些上限都是禁区！⛔

[https://glitch.com/embed/#!/embed/building-rotavo-07?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-07?previewSize=100&path=index.html)

## 计数旋转

如果你是那种天生倾向于立即破解你面前精心编写的代码的工程师，那么你可能会想，“如果`max`高于`scale`会发生什么？”幸运的是，本质上没有任何东西被破坏，但这确实让那个`max`值变得有点无意义，因为我们永远无法达到它。良好的...除非我们能计算出旋转的次数。例如，一整圈让我们到 10，另一整圈让我们到 20，等等。把它想象成一台绞车或一个从井里拉出水桶的曲柄——当你转动曲柄时，绳子会卷进或卷出，直到它到达顶部或底部。

我们不会将`_rotations`作为属性公开，因为它是`value`和`scale`的结果。如果我们真的有它，我们将需要引入一些混乱的优先规则，如果你设置了冲突和...呃，我不碰那个。无论如何，让我们在连接元件时初始化导出的`_rotations`值。

```
attributeChangedCallback(attrName, oldVal, newVal) {
  this._angle = (TWO_PI / this.scale) * (this.value % this.scale);
  this._rotations = Math.floor(this.value / this.scale);
  this._drawState();
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到角度是如何设置的:`_angle`是`value`除以`scale`的余数(或模数)。`_rotations`的个数是`value`除以`scale`的整数值(或商)。这是第一根棉绒上的`a % b`和第二根棉绒上的`Math.floor(a / b)`的配对。

为了跟踪旋转的变化，我们要把元素分成四个象限。任何一个顶部象限之间的移动都将被视为旋转的变化。

[![A round element with quadrants overlaid. A circular path indicates a touch going from the top-left to the top-right quadrant resulting in an increment to the rotation.](img/e52390a8e6c58d8e73ba6cf04c2bba47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kSyQdBdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/35mw7wcsbhby9g6nih32.png)

移入或移出较低的象限只是在同一个旋转中的运动。这种变化意味着我们需要跟踪以前的角度，这样我们在计算新的角度时就有了比较的对象。

在我们看代码之前要考虑的最后一点是，我们的元素现在实际上有两种操作模式。第一个我们已经看到了——旋转旋钮，一旦你越过上面的`scale`,然后你绕到`0`。然而，现在我们正在跟踪旋转，我们有一个`value`，它在每次旋转时都会增加。拥有一个让用户将值增加到无穷大的控件可能不是一个好主意，所以我们应该确保它以某种方式有界。这意味着我们要添加的检查是，如果设置了`min`和`max`值，我们将只跟踪`_rotations`。我知道，我知道——我们目前根本没有验证这些属性...但是，我必须为下一篇文章保留一些内容！

好，让我们一步一步地跟踪旋转的变化:

```
// _rotationChange()
// Grab the previous angle for comparison
this._previousAttemptedAngle = this._attemptedAngle;
this._attemptedAngle = // ✂️ calculate attempted angle

// Track rotations if max and min are set
if (this.max !== null && this.min !== null) {
  // +1 rotation if:
  // new angle is in the top-right quadrant, e.g. < ½π
  // old angle is in the top-left quadrant, e.g. > 1½π
  if (this._attemptedAngle < 1.57 && this._previousAttemptedAngle > 4.71) {
    this._attemptedRotations++;
  }
  // -1 rotation if:
  // old angle is in the top-right quadrant, e.g. < ½π
  // new angle is in the top-left quadrant, e.g. > 1½π
  else if (this._previousAttemptedAngle < 1.57 && this._attemptedAngle > 4.71) {
    this._attemptedRotations--;
  }
}

// New value now includes the rotations
this._attemptedValue =
    (this._attemptedAngle / (TWO_PI / this.scale))
  + (this.scale * this._attemptedRotations);

// Update everything if the value is within bounds
if (
  (this.min === null || this._attemptedValue >= this.min) &&
  (this.max === null || this._attemptedValue <= this.max)
) {
  this._angle = this._attemptedAngle;
  this._rotations = this._attemptedRotations;
  this.value = this._attemptedValue;
} 
```

Enter fullscreen mode Exit fullscreen mode

演示时间！让我们带着它兜一圈(aaay！🥁)在我们的第一个演示中，我们有一个控制 T1 的 T0。将它向后翻转，这样我们就有了一个`<input-knob>`控制一个`<input type="range">`。

我们可以这样设置标签:

```
<input-knob value="50" scale="10" min="0" max="100">...</input-knob>
<input type="range" class="progress" min="0" max="100"> 
```

Enter fullscreen mode Exit fullscreen mode

然后对`knob-move-change`使用同一个监听器，我们更新这些值:

```
// logEvent()
const curValue = Number.parseFloat(knob.value).toFixed(3);
showValue.textContent = curValue;
range.value = curValue; 
```

Enter fullscreen mode Exit fullscreen mode

现在，元素旋转 10 次，应该可以从 0 到💯。我想这是为这次参赛做准备的理想场所。

[https://glitch.com/embed/#!/embed/building-rotavo-08?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-08?previewSize=100&path=index.html)

下一次我们将确保我们的组件是可访问的，因为尽管触摸输入很有趣，但它并不是每个人都可以选择的。

## 🎁额外内容

哦哦，我不能离开你，没有一点奖励，我能吗？所以，继续借用我同事的脸这个有点可疑的传统(谢谢/向杰克·阿奇博尔德道歉)请随意发现当你结束这个的时候会发生什么...《盒子里的杰克》。

[https://glitch.com/embed/#!/embed/jake-in-the-box?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/jake-in-the-box?previewSize=100&path=index.html)