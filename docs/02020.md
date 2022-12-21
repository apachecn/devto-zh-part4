# 一个仅支持 CSS 的布局调试器

> 原文：<https://dev.to/danburzo/a-css-only-layout-debugger-1o9j>

*交叉发布自[我的个人博客](http://danburzo.ro/css-layout-debugger/)。*

我最近碰到了 Gajus Kuizinas 最喜欢的 CSS hack 。我以前使用过基本的 CSS 调试风格(也称为[诊断 CSS](https://meyerweb.com/eric/thoughts/2007/09/07/diagnostic-styling/) ，有点喜欢它们的简单性。我想知道我是否能更进一步。

## 挑战

> **创建一个仅支持 CSS 的元素检查器**:当你悬停元素时，显示它们的边界框，以及它们的后代是如何布局的——所有这些对布局的影响都是最小的。

## 走近

你可以在这个演示页面上看到我想出的[，并跟随我进入思考过程，死胡同，以及我在这个过程中学到的有趣的 CSS 花絮。](https://danburzo.ro/demos/css-only-layout-debugger.html)

### 基本造型

为了显示一个元素的盒子，我们可以`outline`它:

```
* {
    outline: 1px solid red;
} 
```

Enter fullscreen mode Exit fullscreen mode

`outline`属性不会改变布局，因为它被绘制在元素的顶部，而且作者通常只包括聚焦元素的轮廓样式，所以它相对来说是无害的。

为了辨别元素是如何嵌套的，让我们也添加[洋葱皮](https://en.wikipedia.org/wiki/Onionskin)背景:

```
* {
    outline: 1px solid red;
    background: rgba(255, 0, 0, 0.1);
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利！这开始看起来很有希望，但一切都很危险。

### 循环变换色相

为了进一步区分不同嵌套层次的元素，让我们改变轮廓/背景颜色(如 [Gajus 的例子](https://dev.to%5Bfav-css-hack%5D))。`hsl()`符号是目前在 CSS 中获得颜色变化的少数方法之一，是表达我们的颜色的一个很好的选择。

首先，进行一点重构，将`red`和`rgba(255, 0, 0, 0.1)`放在一起:

```
* {
    --hue: 0; /* red */
    outline: 1px solid hsl(var(--hue), 100%, 50%);
    background: hsl(var(--hue), 100%, 50%, 0.1);
} 
```

Enter fullscreen mode Exit fullscreen mode

有了`--hue`自定义属性，以及基于它的轮廓和背景，我们可以简单地给它分配各种值，让一切和谐地变化。

> **注:** HSL 不错，但没有*伟大*。它将面向机器的红色、绿色和蓝色通道替换为人类可以更好地关联的东西——色调、饱和度和亮度。然而，这并不是我们感知颜色的真实方式。保持恒定的饱和度和亮度，各种色调的颜色在人眼看来会变得更亮或更暗。当它在浏览器中实现时，[`lch()`颜色符号](https://www.w3.org/TR/css-color-4/#lab-colors)将提供一个更好的近似。

我们如何以 60 度的增量来循环色调呢？你可能和我一样，认为 CSS 自定义属性的工作方式就像其他编程语言中的普通变量一样，并且达到:

```
* {
    --hue: calc(var(--hue, 0) + 60);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，CSS 不允许自定义属性(根据彼此定义自身的属性集)之间的循环依赖，也不允许包含引用自身的属性。在该规范的未来版本中，我们可能[得到一种方法](https://github.com/w3c/csswg-drafts/issues/1594#issuecomment-382832667)让一个元素基于它从其祖先继承的值重新定义一个自定义属性，但在那之前，没有骰子。

翻着 [CSS 值和单位规格](https://www.w3.org/TR/css-values-4/)，我惊讶地发现[的`toggle()`功能](https://www.w3.org/TR/css-values-4/#toggle-notation)。它使元素能够在一组值之间循环，而不是继承值。你可以写:

```
* {
    --hue: toggle(0, 60, 120, 180, 240, 300);
} 
```

Enter fullscreen mode Exit fullscreen mode

...使每个后续级别的元素远离其父级移动 60 度。漂亮清晰...不支持。尽管`toggle()`的想法早在 1999 年就已经在[中出现，但在撰写本文时还没有浏览器实现它。](http://lists.w3.org/Archives/Public/www-style/1999May/0067)

所以，除非我错过了一个聪明的变通方法，否则我们又回到了老派

```
* { --hue: 0; }
* > * { --hue: 60; }
* > * > * { --hue: 120; }
* > * > * > * { --hue: 180; }
* > * > * > * > * { --hue: 240; }
* > * > * > * > * > * { --hue: 300; } 
```

Enter fullscreen mode Exit fullscreen mode

这个设置的上限是 300 色调(一种漂亮的紫红色)，但是你可以根据你的意愿继续循环选择。

### 显示元素周围的填充

如果到目前为止，浏览器几乎没有为我们所做的任何事情流汗，那么是时候通过我们的下一个小挑战来提高温度了:可视化地显示填充。

我们需要一些东西，让我们能够独立地挂钩到元素的内容框和填充框。你好 [`background-origin`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-origin) ！

属性只适用于背景图片*，而不是背景颜色，所以我们需要一种方法用纯色制作背景图片。 [CSS Images Level 4](https://drafts.csswg.org/css-images-4/) 规范定义了[`image()`语法](https://drafts.csswg.org/css-images-4/#image-notation)，它接受一种颜色，比如`image(fuchsia)`，以生成一幅图像。但是在浏览器支持它之前，我们需要用渐变来即兴创作。*

 *据我所知，用渐变语法获得纯色的最短公式是:

```
* {
    background-image: linear-gradient(fuchsia, fuchsia);
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让内容框和填充框有不同的颜色，我们将两个图像分层，并分别定义它们的`background-origin`:

```
* {
    background-image: 
        linear-gradient(fuchsia, fuchsia),
        linear-gradient(yellow, yellow);
    background-origin:
        content-box,
        padding-box;
    background-repeat: no-repeat;
} 
```

Enter fullscreen mode Exit fullscreen mode

多个背景的顺序是从离用户最近(最上面)到最远。在上面的代码中，内容绑定的 fuchsia 位于填充绑定的 yellow 之上。我从来不记得这个顺序，不得不经常查找。包含`background-repeat: no-repeat`对于它按预期工作是很重要的，这一点我也很容易忘记。

> **注:**用 [`background-clip`](https://developer.mozilla.org/en-US/docs/Web/CSS/background-clip) 代替`background-origin` / `background-repeat`组合大概也能得到类似的效果，不过我还没深入研究过。

为了更酷的外观，就像你有时在开发工具中看到的那样，让我们把黄色填充框变成漂亮的斜条纹。 [`repeating-linear-gradient`](https://developer.mozilla.org/en-US/docs/Web/CSS/repeating-linear-gradient) 对此有用:

```
 { 
    background-image: repeating-linear-gradient(
        45deg, /* angle for diagonals */
        fuchsia, fuchsia 1px, /* our 1px stripes */
        transparent 1px, transparent 3px /* 2px space between stripes */
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们把它写进我们的检查员代码:

```
* {

    /* Opaque version of the color */
    --c-solid: hsl(var(--hue), 100%, 50%);

    /* Translucent version of the color */
    --c-bg: hsl(var(--hue), 100%, 50%, 0.1);

    outline: 1px solid var(--c-solid);

    background-image: 

        /* Content box fill */
        linear-gradient(var(--c-bg), var(--c-bg)),

        /* Content box white underpaint */
        linear-gradient(white, white),

        /* Padding box stripes */
        repeating-linear-gradient(
            45deg,
            var(--c-solid), var(--c-solid) 1px,
            var(--c-bg) 1px, var(--c-bg) 3px
        );

    background-origin:
        content-box,
        content-box,
        padding-box;

    background-repeat: no-repeat;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使组合与条纹一起工作，你可能会注意到我们在上下文框的条纹上添加了一层白色的油漆，以模糊它们。

### 让我们的检查员能够感知悬停

到目前为止，我们已经很好地突出显示了页面上的所有内容。如果我们能够将高亮显示定位到我们所悬停的元素上，就更容易理解了。

小问题:当你在页面上悬停一个元素时，你不只是悬停该元素。你也盘旋着它所有的祖先。CSS 的设计方式不允许我们只选择最里面的悬浮元素。(我们需要知道一个特定的元素何时包含另一个悬停的元素。)

最接近理想状态的方法是将`*`(页面上的每个元素)改为`body :hover, body :hover > *`，这意味着*悬浮的元素和它们的直接后代*。我们也没有设计`body`本身，因为它没有提供太多的信息，而且增加了视觉噪音。

```
body :hover, body :hover > * {

    /* Opaque version */
    --c-solid: hsl(var(--hue), 100%, 50%);

    /* Translucent version */
    --c-bg: hsl(var(--hue), 100%, 50%, 0.1);

    outline: 1px solid var(--c-solid);

    background-image: 

        /* Content box fill */
        linear-gradient(var(--c-bg), var(--c-bg)),

        /* Content box white underpaint */
        linear-gradient(white, white),

        /* Padding box stripes */
        repeating-linear-gradient(
            45deg,
            var(--c-solid), var(--c-solid) 1px,
            var(--c-bg) 1px, var(--c-bg) 3px
        );

    background-origin:
        content-box,
        content-box,
        padding-box;
    background-repeat: no-repeat;
} 
```

Enter fullscreen mode Exit fullscreen mode

**关于`:focus`的一个注解。**不像`:hover`，一次只有一个元素有`:focus`，我们可以围绕它来建模我们的方法。然而，为了使所有的元素都是可聚焦的，并且避免当我们点击它们时触发它们的默认行为，我们需要一些 JavaScript 的帮助:

```
Array.from(document.querySelectorAll('*')).forEach(
    el => {
        el.setAttribute('tabindex', 0);
        el.addEventListener('click', e => {
            e.preventDefault();
            e.stopPropagation();
        });
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

...这削弱了纯 CSS 解决方案的魅力。

### 额外学分:识别元素

最后一个小挑战:当我们悬停在元素上时，我们能显示元素的信息吗——它们的标签名、ID 和类？

CSS 有[和`attr()`函数](https://www.w3.org/TR/css-values-4/#attr-notation)来读取 HTML 元素的属性并在样式中使用它们。在撰写本文时，我们只能使用`attr()`作为`::before`和`::after`伪元素的`content`，但是对于我们适度的目标来说，它会做得很好。我们可以提取元素的`class`和`id`——但不能提取它的标签名——并将它们显示为浮动标签:

```
[id]:hover::before,
[class]:hover::before {
    position: absolute;
    transform: translate(0, -100%);
    background: #000;
    color: hsl(var(--hue), 100%, 80%);
}

:not([id])[class]:hover::before {
    content: '.' attr(class);
}

:not([class])[id]:hover::before {
    content: '#' attr(id);
}

[id][class]:hover::before {
    content: '#' attr(id) '.' attr(class);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们打开它。

`[id]:hover::before, [class]:hover::before`选择器匹配悬停元素的`::before`伪元素，这些元素附加了 ID 或 class 属性。

因为我们不能有条件地设置这些元素的'`content`(基于哪个 ID / class 属性存在)，所以我们为两者都有(`[id][class]`)的元素设置它，并为只有一个(`:not([id])[class]`)或另一个(`:not([class])[id]`)的元素单独设置它。

`position: absolute`将`::before`伪元素从正常流中取出。通过不指定`top`和`left`偏移量，我们将它保留在其父内容框左上角的默认位置。相反，我们使用`transform(0, -100%)`将它向上拉，因此它位于其父对象之上。

**注意:**因为我们接管了`::before`元素，我们可能会破坏依赖于它的布局的某些方面。即便如此，这种技术也不是万无一失的，可能会受益于在父节点上设置`position: relative`和显式的`top`和`left`偏移量，但有进一步改变原始布局的风险。

### 现在都在一起了

下面是代码的最终版本:

```
/* 
    Hue rotation 
    ------------
*/

* { --hue: 0; }
* > * { --hue: 60; }
* > * > * { --hue: 120; }
* > * > * > * { --hue: 180; }
* > * > * > * > * { --hue: 240; }
* > * > * > * > * > * { --hue: 300; }
* > * > * > * > * > * > *{ --hue: 0; }
* > * > * > * > * > * > * > * { --hue: 60; }
* > * > * > * > * > * > * > * > * { --hue: 120; }
* > * > * > * > * > * > * > * > * > * { --hue: 180; }
* > * > * > * > * > * > * > * > * > * > * { --hue: 240; }
* > * > * > * > * > * > * > * > * > * > * > * { --hue: 300; }

/* 
    Draw elements' boxes
    --------------------
*/

body :hover, body :hover > * {

    /* Opaque version */
    --c-solid: hsl(var(--hue), 100%, 50%);

    /* Translucent version */
    --c-bg: hsl(var(--hue), 100%, 50%, 0.1);

    outline: 1px solid var(--c-solid);

    background-image: 

        /* Content box fill */
        linear-gradient(var(--c-bg), var(--c-bg)),

        /* Content box white underpaint */
        linear-gradient(white, white),

        /* Padding box stripes */
        repeating-linear-gradient(
            45deg,
            var(--c-solid), var(--c-solid) 1px,
            var(--c-bg) 1px, var(--c-bg) 3px
        );

    background-origin:
        content-box,
        content-box,
        padding-box;
    background-repeat: no-repeat;
}

/* 
    Show elements' classes / ID
    ---------------------------
*/

[id]:hover::before,
[class]:hover::before {
    position: absolute;
    transform: translate(0, -100%);
    background: #000;
    color: hsl(var(--hue), 100%, 80%);
}

:not([id])[class]:hover::before {
    content: '.' attr(class);
}

:not([class])[id]:hover::before {
    content: '#' attr(id);
}

[id][class]:hover::before {
    content: '#' attr(id) '.' attr(class);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 为了使它更有弹性，我们可以加入一些关键字，但这留给读者去练习。

这里是[再次演示页面](https://danburzo.ro/demos/css-only-layout-debugger.html)。仅此而已！✌️*