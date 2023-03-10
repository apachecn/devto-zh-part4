# 使用 CSS 网格创建一个自定心全角元素

> 原文：<https://dev.to/brob/use-css-grid-to-create-a-self-centering-full-width-element-p46>

[![Self Centering Grid with firefox grid inspector lines showing one column centered](img/f1a0062f6fba625acac3a41fbe8bb2be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WfR-NEwj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.cimg/self-center-grid.png)

有时候，一个想法如此强烈地冲击着你，以至于你不知道为什么你从来没有想到过它。CSS Grid 擅长于[创建新类型的布局](https://bryanlrobinson.com/blog/2019/04/10/grid-vs-flex-tale-of-simple-promo-space/)。它在简化旧设计模式的代码方面也令人惊叹。

当我在一个带有全幅背景色的条纹内创建一个居中的文本列时，一个想法悄悄出现在我的脑海中。

传统上，这可以通过在我们的标记中添加一个额外的`<div>`来解决。HTML 看起来会像这样。

```
<section class="stripe">
    <div class="stripe__content">
        <h1>Hello Stripe world</h1>
        <p>More stripe content can go here ...</p>
    </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

我们使用外部的`<section>`来应用背景颜色，内部的`<div>`来调整内容的大小和居中。这绝不是涨价危机。CSS 也相对干净。

```
.stripe {
    background-color: lavender;
    padding: 2rem 0;
}
.stripe__content {
    width: 90vw;
    max-width: 1200px;
    margin: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这不是一个很大的越轨行为，所以我从来没有想过为此目的使用 CSS Grid。我曾经使用 Grid 在其他设计中创建全幅条纹，但从来没有这么简单。

这个应用程序的闪电让我完全措手不及。即使属性是`grid-template-columns`，我们也可以将它用于单个列。配上`justify-content`和一点点填充物，瞧！

一个彩色条纹，带有一组宽度受限的内容，没有附加标记！

```
<section class="stripe">
    <h1>Hello Stripe world</h1>
    <p>More stripe content can go here ...</p>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

```
.stripe {
    display: grid;
    grid-template-columns: minmax(auto, 1200px);
    justify-content: center;

    background-color: lavender;
    padding: 2rem 1rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法为我们的网格创建一列。它的最小尺寸为`auto`以允许它根据其内容缩小，最大尺寸为 1200 像素。这将创建适当大小的元素。我们用`justify-content`来代替处理`auto`的边距。在这个方法中，我们需要为我们的移动宽度进行左/右填充。

从盒子模型的角度来看，这很有意义。填充用于内部间距。在我们的老方法中，我们使用的是总感觉有点粗糙的边距。

我从未想过这个用例，但当我明白过来时，它变得如此有意义。随着网格成为我们主要布局机制之一的未来的到来，我们将会看到更多这样的应用。

Grid 不仅仅适用于复杂的布局，它也适用于灵活的简单布局。有哪些其他的布局乍一看不像你会使用网格，但网格会做得更好？

[https://codepen.io/brob/embed/eqdZdm?height=600&default-tab=result&embed-version=2](https://codepen.io/brob/embed/eqdZdm?height=600&default-tab=result&embed-version=2)

### 与布莱恩保持联系

如果你想及时了解我制作的所有内容，请在这里关注我，同时也关注其他平台。

*   [推特:@brob](https://twitter.com/brob)
*   [YouTube 上我的视频](https://goo.gl/wCeu5C)
*   [我的 Patreon 提前更新](https://patreon.com/brob)
*   [在 peerreviews.dev 上观看或请求设计或代码评审](https://peerreviews.dev)