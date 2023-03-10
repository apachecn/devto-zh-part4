# CSS 中的度量单位

> 原文：<https://dev.to/cole_ruche/units-of-measurements-in-css-ap2>

*最初发表于我的[博客](https://colesblog.netlify.com/post/Units-of-Measurements-in-CSS/)T3】*

*px 和%，em 和 rem——它们并不都一样*

自从我学习并开始使用 CSS 设计页面样式以来，我总是对使用哪种单位来设计段落、标题、填充和边距感到困惑。这在(前端)开发人员中很常见，因为有相当多的选项可供使用——比我开始写这篇文章之前所知道的选项还要多！我一直推迟学习这些单元之间的差异，因为我认为它们并不重要，直到[凯拉](https://twitter.com/_KaylaSween)在推特上创造了[一个小测验](https://twitter.com/_KaylaSween/status/1153799464340971525)。我失败了。

正是在这一点上，我决定试着去理解它们都是什么，并把它们记录下来。

### 那么它们是什么呢？

单位分为 ***绝对*** 和 ***相对*** 长度。让我们一个接一个地看。

### 绝对长度

这些是固定的长度单位，显示的正是那个大小。绝对单位最不鼓励在屏幕上使用，因为大屏幕上的 12px 在 iPhone 上可能看起来不一样。它们高度依赖于输出介质，因此应该*理想情况下*在输出介质已知或目标屏幕尺寸特定时使用。

> “除了印刷之外，还有一个原因可以避免使用绝对单位:你从不同的距离观看不同的屏幕。1 厘米的桌面屏幕看起来很小。但是同样在手机上直接在你眼前看起来就大了。最好用相对单位，比如 ***em*** 来代替。”
> 
> ——*w3.org*

绝对单位有: ***px*** ，***cm***， ***pt*** ， ***pc*** ， ***mm*** 。可能还有其他的，但为了简洁起见，我们将只讨论这些。

*   ***px*** :定义屏幕像素大小。它相当于电脑屏幕上的一个点。一个像素等于 1/96 英寸。 [W3](https://w3.org) 将 ***px*** 描述为*CSS*的魔法单位。
*   中的 ***:这是英寸的意思。1in 等于 96px。***
*   ***厘米*** :厘米。等于 37.8px。
*   ***毫米*** :毫米。1 毫米与 3.778 像素相同
*   ***、pc*** 、 ***、pt*** :分别以点为单位定义度量。1pt 等于 1/72 英寸，而 1pc 等于 12pt。

### 相对长度

这些单位相对于另一个长度属性定义大小和长度。它们是最受欢迎的，因为它们可以更好地跨越不同的媒介。它们包括 ***em*** ， ***rem*** ， ***%*** ， ***ex*** ， ***ch*** 等等。

*   ***em*** :简单来说，an ***em*** 与当前字号相同。默认情况下，现代浏览器以 16px (12in)显示字体，因此，以 1em 显示的文本与 16px 相同。例如，请看下面的样式:

```
p {
    font-size: 2em
} 
```

Enter fullscreen mode Exit fullscreen mode

这将字体大小设置为 32px，即 2 * 16px。

***em*** 是可扩展的，并且是移动设备友好的。

*   ***雷姆*** :代表“***r***oot***em***”。这是相对于根元素的字体大小，根元素是`html`标签。考虑下面的风格:

```
html {
    font-size: 62.5%;
}
body {
    font-size: 100%;
}
p {
    font-size: 1rem
} 
```

Enter fullscreen mode Exit fullscreen mode

`p`标签的大小是 62.5%(16px，即 10px)，而不是 100% (16px)，因为它是相对于`html`字体大小而不是 body 标签的大小。
`rem`单元没有被广泛使用，所以你可能不需要使用它。

*   ***%*** :定义相对于父元素的大小。
*   ***ex*** 和***ch***:***ex***定义了相对于字体的 x 高度的度量，即字体的小写字母“x”的大小，而 ***ch*** 是相对于字符“0”(零)的字体宽度。

### 结论

真的，选择用什么单位是留给你自己选择的，但是大部分人都是用 ***em*** 和 ***px*** 。

> “我倾向于使用***【px】***作为边框，使用 ***rems*** 作为大部分其他的边框——因为这样很容易保持事物的一致性，并且不需要太多的努力就可以调整布局。”
> 
> 布莱恩·沃恩

JavaScript Joe 和 JavaScript Joel 似乎都是 px 的粉丝。

> “当谈到字体大小时，我传统上使用类似[的字体，因为它基本上为我建立了一个系统。
> 
> 然而，当我使用像素时，我尽量坚持使用 4px 的倍数(例如 4px、8px、12px、16px 等)来度量。这样东西感觉“统一”。](https://kyleamatthews.github.io/typography.js/)
> 
> [JavaScript 乔](https://twitter.com/jsjoeio)

> “我通常只使用 px。这可能是我的偏见，因为当我开始时，px 是唯一绝对可用的东西。现在我让设计师来告诉我这是什么风格。”
> 
> - [JavaScriptJoel](https://twitter.com/joelnet)

这是推特用户弗罗林说的话:

> “我大多用 px。不太会和别人相处。”
> 
> - [弗罗林流行](https://twitter.com/florinpop1705)

现在，无论您选择使用哪种方法，都建议正文文本至少使用 16px(1em ), em 作为首选单位。这确保了字体大小与默认字体大小相关，默认字体大小是读者可以轻松阅读的大小。它还确保了尺寸在不同的屏幕尺寸和密度上能够很好地缩放。虽然我们可以选择忽略这一点，但由于现代浏览器和设备提供了增加字体大小和显示的工具，所以在设计时考虑 a11y 不会有什么坏处。

正如 [Brent Clark](https://twitter.com/Brent_m_Clark) 正确指出的，使用`em`也有它自己的问题:它与父代的大小有关，而父代又依赖于其他父代。例如，考虑下面的代码:

```
<!-- HTML -->
<body>
    <p class='outer-p'> 
        Outer paragraph
        <p class='nested-p'> Nested paragraph</p>
    </p>
</body>

<!-- CSS -->
<style>
    body{font-size: 16px}
    .outer-p{font-size: 2em}
    .nested-p{font-size: 1.5em}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

从上面的代码中，我们需要将`outer-p`段落设为 2em，并且正确的是，它将是 32px，因为它将是直接父体 body 的 x2，body 已经被设置为 16px。

现在，如果想要将我们的`nested-p`段落设置为 1.5em，并期望它输出为 24px(即 1.5 * 16px)，我们将会失望，因为大小输出为 48px。这种行为是因为`em`是相对于父母的大小而言的。在本例中，`nested-p`是`outer-p`的子节点，它的大小已经是 32px。这解释了为什么它是 48px，即 1.5 * 32px。

使用时要考虑到`em`的这种行为。

为了避免可能与`em`相关的不想要的结果，我相信这就是为什么引入了`rem`。这样，只需直接在`html`上设置默认样式，然后使用`rem`作为你的 CSS 单位，这样你就可以确定它们都是指`html`标签的样式大小，而不是它们父标签的大小。

例如，使用上面相同的代码，为了获得期望的`outer-p`和`nested-p`的大小，分别是 32px 和 24px，我们将*将*重构为:

```
<!-- HTML -->
<body>
    <p class='outer-p'> 
        Outer paragraph
        <p class='nested-p'> Nested paragraph</p>
    </p>
</body>

<!-- CSS -->
<style>
    html{font-size: 16px}
    .outer-p{font-size: 2rem}
    .nested-p{font-size: 1.5rem}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，字体大小现在都相对于根样式，在本例中为`html`。

至于我，我会*尝试*坚持我发现的这个小片段:

```
body {
    font-size: 62.5% /* sets default font size to 10px i.e 62.5% of 16px */
}
p {
    font-size: 1.4em  /* 14px */
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我之前提到的，使用任何让你舒服的东西。如果你没有真正掌握它，不要担心，我仍然没有掌握它的大部分。

如果 1.4em 导致 14px，为什么不用 14px 呢？