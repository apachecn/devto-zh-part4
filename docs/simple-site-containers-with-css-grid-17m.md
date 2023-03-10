# 带有 CSS 网格的简单站点容器

> 原文：<https://dev.to/iangloude/simple-site-containers-with-css-grid-17m>

每个站点都需要某种容器。这里有一种方法可以利用 CSS Grid 来轻松地将内容放入和取出页面容器。我们将努力复制这个例子:

[![CSS Grid Container Example](img/5e5ce33f22de0fb867b47e02f1100c97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uF8V72qI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ojq11g2nq1omtg7rgyh8.png)

我们有一个文本列，位于页面中央，还有一个高亮显示的部分，具有全幅背景，文本与其他部分水平对齐。

## 容器

要为我们的内容创建一个居中的列，我们需要在父网格中有三列。但在此之前，我们需要调用 CSS 网格的黑暗，神秘的力量。紧紧跟随咒语:`display: grid;`

明白了吗？太好了。魔法毕竟没那么难。

## FR 单位

接下来，我们需要理解 CSS 的另一个神奇部分:`fr`(小数)单元。该单元的值是其 fr 值与其父单元的所有子单元之间的关系。

> 例如:
> 
> 如果我们有一个 300 像素宽的父元素，那么一个 1fr 宽的子元素也是 300 像素宽。两个宽度为 1fr 的孩子将各为 150px。一个孩子有 1fr，第二个孩子有 2fr，分别是 100px 和 200px。

## Minmax

我们将使用的最后一个构件是`minmax()`。该函数有两个参数，工作起来就像听起来一样。在一列上使用`minmax(10px, 100px)`会使它至少有`10px`宽，但不会超过`100px`宽。

在本文中我们不会深入探讨它，但是将`minmax()`与 CSS 自定义属性结合在一起是非常令人满意的。

## 建筑立柱

使用`grid-template-columns`将列显式分配给网格元素。为了得到一个响应性的布局，我们将让`minmax()`开始工作。

```
.container {
  grid-template-columns:
    minmax(1em, 1fr)
    minmax(0, 30em)
    minmax(1em, 1fr);
} 
```

上面的代码创建了三列。第一个和第三个在`1em`和`1fr`之间是灵活的。中间列的宽度将在 0 到 30em 之间。在这种情况下，如果视口宽度超过 32em (1em + 30em + 1em)。第一列和第三列将占用等量的剩余空间，从而创建一个居中的容器。

你可能会问为什么不是`max-width: 30em;`和`margin: 0 auto;`？坚持住。

## 命名栏目

我们有 3 列，4 列线，我们可以对齐内容。为了在中间的列中包含一个元素，我们将把`grid-column: 2 / 3;`添加到`.container`的子元素中。

为了简化，我们可以用下面的语法给网格线命名:`[linename-start]`和`[linename-end]`。将行名添加到我们的模板中会导致:

```
.container {
  grid-template-columns:
    [viewport-start] minmax(1em, 1fr)
    [container-start] minmax(0, 30em) [container-end]
    minmax(1em, 1fr) [viewport-end];
} 
```

## 听话的孩子

现在我们可以带着容器的孩子，告诉他们住在哪里。以及该怎么做。以及什么时候睡觉。

```
.item--full {
  grid-column: viewport;
}
.item--contained {
  grid-column: container;
} 
```

您可能已经猜到，`.item--full`将跨越所有三列的宽度，而`.item--contained`将只跨越中间一列。

## 继承

布局的最后一个技巧是将元素内部的内容与`.item--full`类对齐。我们需要将父网格传递给`.item--full`子网格。

```
.item--full {
  grid-column: viewport;
  display: inherit;
  grid-template-columns: inherit;

  > * {
    grid-column: container;
  }
} 
```

现在，`.item--full`类是它自己的网格容器。因为它跨越了父网格的整个宽度，所以得到的嵌套网格实际上是放在它的父网格之上的副本，并在每个直接后代上添加了声明`grid-column: container;`。

看看最后的结果:
[https://codepen.io/igloude/embed/pPVPxP?height=600&default-tab=css&embed-version=2](https://codepen.io/igloude/embed/pPVPxP?height=600&default-tab=css&embed-version=2)

开心烤盘！