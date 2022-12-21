# HTML 布局的不完整指南

> 原文：<https://dev.to/jacobmparis/an-incomplete-guide-to-html-layouts-1din>

在 HTML 中构建布局从未如此简单，但是为了快速有效地设计页面，您仍然需要了解一些基础知识。第一步是了解网页如何呈现不同的元素。

我们将在这里了解四种主要的显示类型。

### 块

块元素是垂直堆叠的全宽容器。你可以给他们尺寸，比如高度、宽度和边距。两个连续的块元素将垂直堆叠，即使有空间让它们并排放置。

最基本的块元素是`<div />`

### 内嵌

在溢出到下一行之前，行内元素像文本一样水平流动。如果将一个大的行内元素拆分成两个连续的行内元素(例如，将一个段落拆分成单独的句子)，它的布局不会改变。

您不能设置行内元素的宽度或高度。

最基本的内联元素是`<span />`

如果你给一个`span`CSS 属性`display: block;`，它将和一个`div`元素行为相同，反之亦然。这两个基本元素之间唯一重要的区别是浏览器的默认显示类型。

### 内嵌块

内联块元素像文本一样流动，但是您可以给它们宽度和高度值。只要有空间，两个连续的内联块元素将水平排列，否则它们将溢出到下一行。

### 伸缩

这是最新和最复杂的显示类型之一，但是非常强大。当您将容器设置为`display: flex`时，它的子容器将根据您设置的规则进行排列。

在容器上，你最重要的规则是`flex-direction`、`align-items`和`justify-content`。

`align-items`设置孩子在*横轴*上的位置，因此默认情况下它作为垂直对齐。如果您的伸缩方向设置为列，那么它将改为水平对齐。大多数时候你会想把它设置为`center`

`justify-content`设置*主轴*上的子节点位置，水平方向为行，垂直方向为列。`space-around`、`space-between`、`space-evenly`在这里会是你的亮点，但是你还有`start`、`center`、`end`可以用。

还有许多其他的规则，比如对齐内容、对齐自我、调整自我、调整项目——但是你不需要关心这些。它们没有被浏览器很好地实现，它们没有做任何有意义的事情，这些事情是你不能用上面的两个选择器来做的。

对于子节点，最重要的规则是`flex`，它是其他三个属性的组合:伸缩、伸缩和伸缩。前两个是标志。如果希望元素能够自动扩展，请将 flex grow 设置为 1。Flex shrink 允许它根据需要缩小尺寸。弹性基准是元素将占用的空间量。

会给你一个占据其容器 30%的元素，但如果需要可以缩小以容纳更多的元素。

# 单列页面

单栏布局是网页设计之王。它不仅可以自然地扩展到移动设备，而且干净、集中，能很好地完成所有需要做的事情。

在移动设备上，我们希望它占据整个屏幕，但在大型桌面上，我们希望它更窄，所以我们可以使用 width 和 max-width 属性来实现这一点。

因为我们是全角的，`display: block`是这里的关键，默认情况下`<div>`元素已经被阻塞，所以我们不需要手动指定。

```
.page {
  width: 100%;
  max-width: 50rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/jacobparis/embed/zVeQEJ?height=600&default-tab=result&embed-version=2](https://codepen.io/jacobparis/embed/zVeQEJ?height=600&default-tab=result&embed-version=2)

# 横向列表

我们希望列表元素并排，这意味着`inline`和`inline-block`是我们的朋友。

```
.horizontal-list li {
  display: inline-block;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/jacobparis/embed/ZdwNZq?height=600&default-tab=result&embed-version=2](https://codepen.io/jacobparis/embed/ZdwNZq?height=600&default-tab=result&embed-version=2)

# 两栏布局(侧栏和内容)

```
.content {
  display: flex;
}

.column {
  flex: 0 1 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<div class="content">
    <div class="column" />
    <div class="column" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/jacobparis/embed/xoBbyp?height=600&default-tab=result&embed-version=2](https://codepen.io/jacobparis/embed/xoBbyp?height=600&default-tab=result&embed-version=2)

# 在窗口中居中显示内容

```
body {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/jacobparis/embed/qzvEQY?height=600&default-tab=result&embed-version=2](https://codepen.io/jacobparis/embed/qzvEQY?height=600&default-tab=result&embed-version=2)

### 更多例子接踵而至