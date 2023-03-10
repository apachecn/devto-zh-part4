# CSS Quickies:背景-过滤器

> 原文：<https://dev.to/lampewebdev/css-quickies-backdrop-filter-2f6d>

### 什么是 CSS Quickes？

我开始在 Instagram 上问我心爱的社区:“有哪些 CSS 属性让你感到困惑？”

在“CSS Quickies”中，我将深入解释一个 CSS 属性。这些是社区请求的属性。如果你也对 CSS 属性感到困惑，那么给我写信，在 Instagram 或者 T2 的 Twitter 或者下面的评论里。我回答所有问题。

我还一边在 [twitch.tv](https://www.twitch.tv/lampewebdev/) 上编码一边直播。如果你想直接找我谈！

### 我们来谈谈:`backdrop-filter`

于是来自 Instagram 的`@kamil.codes`挑战我是否知道这个 CSS 属性。我不得不说我不知道那笔财产，或者我忘了！

在我们进一步讨论之前，我想先提一下这一点，因为这对我来说是最大的不幸，Firefox 不支持这个属性！因此，如果你正在试用这些示例，你需要在 Chrome 76+、Edge 17+或 Safari 9+中打开它们才能看到它们。为了确保您的浏览器支持该功能，请查看[can use](https://caniuse.com/#feat=css-backdrop-filter)表。所以这离生产就绪还差得很远。

#### 那么`backdrop-filter`做什么呢？

它可以让你在你使用的元素后面的任何东西上应用效果/滤镜！它不会应用于文本或该元素内部的元素。那就是`backdrop-filter`里的`background`。要查看效果，元素不能应用任何背景色。如果你将`background-color`设置为`white`，那么你只会看到白色而不会有任何效果！

### CSS`backdrop-filter`有什么滤镜？

#### 模糊()

这是你的标准模糊函数。该值越高，模糊看起来就越高。

#### 亮度()

默认值为 1 或 100%。如果你想让图片变暗，你需要把它设置为低于 100%的值，如果你想让它变亮，你需要把它设置为高于 100%

#### 对比()

这些值的作用与 brightness 属性相同。亮度之间的区别在于，如果你让图片更亮，亮度会将每个像素增加到更亮的颜色。另一方面，对比度将增加已经较亮的区域并减少较暗的区域。

#### 阴影()

这是一个有点难以解释，可能是自己的职位！它基本上像任何其他投影一样工作。如果你现在真的想知道，请看看这里

#### 灰度()

灰度图片表示图片中的光线量。换句话说，唯一允许的颜色是黑色、灰色和白色。其中黑色意味着该像素是完全黑暗的，并且在该图片中具有最强的光强度。灰色是黑色和白色之间的强度。将灰度值设置为 0 会保持背景不变。1 或 100%表示此图片完全为灰度。一切将根据值混合图片。

#### hue-rotate(deg)

下面的解释是对色相的过度简化。理解这个属性就够了。想一圈。这个圆的度数从 0 度到 360 度(是的，我知道从技术上讲，它只有 360 度)。现在认为从 0 到 360 的每个值都有一种颜色。其中 0 度为红色，120 度为绿色，240 度为蓝色，依此类推。想象你的图片现在有一个红色像素，换句话说，它的色相值是 0。现在我们将圆旋转 120 度，红色像素变成绿色。这就是为什么叫`hue-rotate`的原因。

#### 反转()

这个正在做你认为它在做的事情。它会反转颜色。黑必白，白必黑。其余的颜色就比较难弄清楚了。为此，您需要了解色轮的外观和工作原理。因为这对于这个 CSS quickie 来说太深了。我送你这个[色轮](https://www.sessions.edu/color-calculator/)。选择第一个和声，然后你就可以摆弄颜色了，这样你就能感受到反转的效果了。

#### 不透明度()

不透明是透明的另一种说法。1 或 100%是默认值，表示背景根本不透明。0 表示背景不可见。比如 80%，就是能透过背景看一点点等等。

#### 棕褐色()

我们都知道棕褐色，对吧？这是一个即使是最便宜的图像处理软件都有的效果。它大约有 130 年的历史，实际上是一种让黑白照片更温暖的方法。这也有助于将照片存档更长时间，因为用于达到这种效果的化学物质需要直接应用在相纸上。

#### 饱和()

啊，饱和度是每个人都使用的效果，也是最容易过度的。将此值设置为 1%或 100%不会有任何效果。当我们将值设置为 2 或 200%时，乐趣就开始了，这意味着饱和度加倍。这意味着，例如，红色现在将有两倍的强度(纯度)！因此，每个像素都将像以前一样成为眼睛的两倍。将此值设置为 0.5%或 50%将从颜色中移除强度，并且一切看起来更灰色和混合。小心这一个

#### URL(file . SVG # filter)；

这是一个更高级的话题，很难在这个 quicky 中解释。如果你想知道更多这方面的信息，请告诉我！如果你现在需要了解一下，可以在这里了解: [SVG 过滤器](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/filter)。

### 一些例子

请检查您的浏览器是否支持`backdrop-filter`。

#### 模糊对比度饱和

正如你所看到的，我们可以结合多个过滤器，使效果更加美丽。
[https://codepen.io/lampewebdev/embed/bPmBxO?height=600&default-tab=result&embed-version=2](https://codepen.io/lampewebdev/embed/bPmBxO?height=600&default-tab=result&embed-version=2)

#### 反转

[https://codepen.io/lampewebdev/embed/mZzRzY?height=600&default-tab=result&embed-version=2](https://codepen.io/lampewebdev/embed/mZzRzY?height=600&default-tab=result&embed-version=2)

#### `hue-rotate`

[https://codepen.io/lampewebdev/embed/GbYWMZ?height=600&default-tab=result&embed-version=2](https://codepen.io/lampewebdev/embed/GbYWMZ?height=600&default-tab=result&embed-version=2)

#### 棕褐色灰度

[https://codepen.io/lampewebdev/embed/Wqappj?height=600&default-tab=result&embed-version=2](https://codepen.io/lampewebdev/embed/Wqappj?height=600&default-tab=result&embed-version=2)

### 资源

*   [背景过滤 CSS 属性](https://css-tricks.com/the-backdrop-filter-css-property/)

*   [MDN 背景滤镜](https://developer.mozilla.org/en-US/docs/Web/CSS/backdrop-filter)

**感谢阅读！**

说你好！[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)