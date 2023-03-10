# 今天我学习了如何用 CSS(和 JavaScript)制作文本渐变动画

> 原文：<https://dev.to/tchaflich/today-i-learned-how-to-animate-a-text-gradient-in-css-and-javascript-2ehp>

Clem Onojeghuo 通过 Unsplash 拍摄的封面照片。

### 非常抱歉。不客气。

[https://codepen.io/tchaflich/embed/rEjrrE?height=600&default-tab=result&embed-version=2](https://codepen.io/tchaflich/embed/rEjrrE?height=600&default-tab=result&embed-version=2)

看那个坏小子*突突*。我想我让我的 CPU 着火了...可怜的东西真的尽力了。我明白为什么 CSS 大佬们现在不想让我这么做了。

## 第一部分:获取文本渐变🌈

您可能会注意到这部分代码:

```
@mixin lead($one, $two, $three, $four, $five, $six) {
  background: linear-gradient(80deg, $one, $two, $three, $four, $five, $six);
  background-clip: text;
  text-fill-color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是这场灾难中真正产生“彩虹文本”的部分。

`background`部分只是生成渐变本身；你以前可能在别处见过的彩虹呕吐。去掉 mixin 中所有的`background-clip`和`text-fill`诡计，它看起来像这样:

[![The text "happy pride month" in black with a rainbow gradient background.](img/af7ed5bbf9e67a20580ec0c1e79f6d5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--20K-24u9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tp7qdpfcylxytg7ipxd3.PNG)

#### 使文本看起来像背景的所有其他东西。

通常情况下，[背景剪辑属性](https://developer.mozilla.org/en-US/docs/Web/CSS/background-clip)用于微调边框和填充等周围背景的外观，但是“文本”值非常神奇。

文本填充颜色的*大致相当于你的标准属性`color`。(对于这支笔，你可以很容易地用`color: transparent`代替，我试过了，效果不错。)在这种情况下，我们可以将其设置为“透明”,这样我们的背景就可以显示出来。*

所以这里得到“彩虹文本”的情况是:

*   背景变成了彩虹
*   背景被剪裁，以便只显示通常被文本覆盖的部分
*   文本是透明的，所以我们可以透过它看到背景

## 第二部分:你可以制作一个背景动画，但是不能制作成线性渐变

这是一切开始偏离轨道的地方。我的第一个方法是在一个单独的容器上打上`transition`，然后结束一天的工作；这将需要大约五分钟，其中大部分是谷歌如何使背景渐变剪辑。

***然而***

[https://codepen.io/tchaflich/embed/bPgmmB?height=600&default-tab=result&embed-version=2](https://codepen.io/tchaflich/embed/bPgmmB?height=600&default-tab=result&embed-version=2)

是时候尝试另一种策略了。

```
<div id="main" class="container lead-red">
  HAPPY PRIDE MONTH
</div>
<div id="fade" class="container lead-orange">
  HAPPY PRIDE MONTH
</div> 
```

Enter fullscreen mode Exit fullscreen mode

等等，为什么有两个-

### 🤔

```
function intervalFunction() {
  rainbowify();
  setTimeout(intervalFunction, getNextTimeoutDuration());
};

intervalFunction(); 
```

Enter fullscreen mode Exit fullscreen mode

你在幕间休息时做什么-

# 🤔 ...🤔 ...🤔 ...

*哦不*

要点如下:

有两个几乎相同的 HTML 元素相互重叠。第一个，`#main`，在最底层，一直可见；它在渐变之间以恒定的不透明度“闪烁”。第二个，`#fade`，在顶层，不断闪烁(对齐时)和淡出(使用`opacity`实现过渡的外观)。

这两个不在同一个“彩虹周期”上——文本`#fade`比文本`#main`领先一种颜色。JavaScript 运行一个循环，使用 setInterval 来处理这两个元素上的类，以保持颜色移动。

那也没用。

## 第三部分:闪现，淡出

我的代码看起来是这样的:在主 setInterval 循环中，尝试用一个将转场计时属性设置为 0 毫秒的`.halt`类来暂停动画(有效地禁用转场)。然后，我将不透明度设置为 1，让它“闪烁”，并删除`.halt`类。我会将不透明度设置回 0，让过渡发挥它的魔力。这一切立刻发生了，只用了大约四行代码。

嗯， [CSS 转换不是那样工作的](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions#JavaScript_examples)。结果是，为了让它转换，渲染引擎需要几毫秒的时间来完成它的动作，而不管当时元素的转换属性是什么。

事实证明，几乎立即添加和删除一个类是不够的。

在放弃并尝试 JavaScript 之前，我曾经尝试过过渡时间和其他 CSS。最初的 JS 攻击是在我现有的 setInterval 循环中使用一个`setTimeout( ... , 20)`,成功了...大约 95%的时间。将超时设置得较低会导致过渡跟不上而断断续续，而将超时设置得较高会导致动画中非常明显的停顿。然而，到处都是奇怪的神奇数字和 Blinky McBlinkerton 的偶尔造访并不是我想要离开 T2 的地方...

## 第四部分:减少抖动

我想消除的第一部分是神奇的 20 毫秒超时。我在谷歌上搜索了很久，找到了这个:

[在删除和添加类名之间触发回流。](https://css-tricks.com/restart-css-animation/#article-header-id-0)

说明了这一点:

```
fade.classList.add("halt");
fade.classList.add("hide");
fade.classList.remove("lead-" + rainbow[(i + 1) % ilen]);
fade.classList.add("lead-" + rainbow[(i + 2) % ilen]);

void fade.offsetWidth; // <- this one!

fade.classList.remove("halt");
fade.classList.remove("hide"); 
```

Enter fullscreen mode Exit fullscreen mode

下一件我认为我应该提前计划的奇怪的事情是 [JS 定时器漂移](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified)。我以前在制作日程表和时钟时见过这种情况；规定的一毫秒在现实中并不总是*一毫秒，因此任何间隔都不可避免地会离准确度越来越远。因为我在我的 SCSS 和我的 JS 中硬编码了相同的超时，如果它们能一致地排队，看起来肯定会更好。这可以防止进一步的停顿、口吃等。由于定时器漂移。*

为此，我使用 setTimeout 而不是 setInterval，并让 Timeout 调用一个函数，该函数调用另一个超时(有效地创建一个超时间隔)。每次超时都会记录其开始时间，并记录与上次超时的“偏差”,然后自我修正以尝试更准确地达到长期目标。如果我转向类似于`@keyframes`的东西，这肯定会很有用。

# 总之:

[![Thomas the tank engine side-eyeing the distance, captioned "Thomas had never seen such a mess."](img/190ca4c9f4c2902fe0587ec7e3320185.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kKwABDgG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://data.whicdn.cimg/300811981/large.png)

这是不必要的复杂和运行像糖蜜在冬天。就做个 gif 什么的。

(ETA:后续[此处](https://dev.to/tchaflich/how-to-actually-animate-a-text-gradient-in-css-3a33)。如果出于某种原因你想*实际上*这样做...)

但我做到了，CSS 霸主们。我打败了你。我赢了。