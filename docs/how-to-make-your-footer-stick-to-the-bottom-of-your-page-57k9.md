# 如何让你的页脚贴在页面底部

> 原文：<https://dev.to/selbekk/how-to-make-your-footer-stick-to-the-bottom-of-your-page-57k9>

当你创建这个为大量内容而设计的精彩设计时，你难道不讨厌它吗？然后你偶然发现一个页面，它只是没有将页脚一直推到页面底部的内容。

大概是这样的:

[https://codepen.io/selbekk/embed/qBWqWQQ?height=600&default-tab=result&embed-version=2](https://codepen.io/selbekk/embed/qBWqWQQ?height=600&default-tab=result&embed-version=2)

看到底部那个可怕的白色缺口了吗？布雷格。

我们如何将页脚贴在屏幕底部？

幸运的是——一旦你学会了怎么做，修复就很容易了。这篇文章将教你如何一劳永逸地避免这种难看的“页脚差距”。🎉

## 第一:内容结构

我工作过的大多数网站都有一个非常简单的基本结构。它们有一个外部容器、一个站点页眉、一个内容区域和一个站点页脚。这是一种通用的流行设计，大多数网站都有这种设计的变体。

您可以用下面的 HTML 创建这个结构:

```
<div class="site-container">
  <header class="site-header">...</header>
  <main class="site-content">
    ...
  </main>
  <footer class="site-footer"></footer>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 然后:CSS！

我们将使用 CSS 布局算法 [flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox) 把那个讨厌的页脚推到它应该在的地方。

首先，我们将以下样式应用于我们的`site-container`——封装了整个网站的包装`<div />`:

```
.site-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们指定主容器应该使用 flexbox 进行布局，并且其子容器应该垂直堆叠。我们还指定站点容器应该至少是屏幕的高度。

其次，我们必须对我们的`site-content` :
再应用一个 CSS 规则

```
.site-content {
  flex: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

`flex: 1`是一种快速的说法，表示项目应该在它的容器中尽可能地变大。

就是这样！

这是最终结果的代码笔:

[https://codepen.io/selbekk/embed/PoYbooN?height=600&default-tab=result&embed-version=2](https://codepen.io/selbekk/embed/PoYbooN?height=600&default-tab=result&embed-version=2)

希望你觉得这有用！:)