# 响应设计的柔性盒布局(第一部分)

> 原文：<https://dev.to/gvinod1991/flex-box-layout-for-responsive-designs-part-1-24co>

在设计中，网页或屏幕布局设计是最重要的部分。如果我们能够很好地设计布局，那么我们就可以按照要求将项目放入布局/容器中。为了设计布局，我们通常使用显示、定位和浮动。在此布局模块之前，我们使用了表格布局。现在我们在 CSS 布局中有了一个新的特性，那就是显示:flex，换句话说，灵活的布局。我们可以在容器中的项目之间对齐和分配空间，即使项目的大小是未知的或动态的。

Flex-box 布局模块有助于开发人员在不使用浮动和位置的情况下轻松开发响应性布局。

让我们深入了解 flex-box 布局或灵活布局的更多细节。

从 display flex 开始，我们需要用 display: flex 定义容器。

```
.container{
    display:flex;
}

<div class="container">
    <div>1</div>
    <div>2</div>
    <div>3</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

要水平对齐项目，请使用对齐内容。它接受以下值。

*   flex-start:项与容器的左侧对齐
*   flex-end:项与容器的右侧对齐
*   居中:项目在容器的中心对齐
*   间距:项目之间以相等的间距显示
*   周围空间:项目周围显示相等的空间

参见下面的代码

```
.container{
    display:flex;
    justify-content:flex-start;/*Item align at beginning of the container horizontally*/
}
<div class="container">
    <div>1</div>
    <div>2</div>
    <div>3</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

若要垂直对齐项目，请使用 align-item。它接受以下值。

*   flex-start:项目与容器的顶部对齐
*   flex-end:项目与容器的底部对齐
*   居中:项目在容器的中心对齐
*   基线:项目显示在容器的基线上
*   拉伸:拉伸项目以适合容器

参见下面的代码

```
.container{
    display:flex;
    align-item:flex-end;/*Item align at the bottom vertically*/
}

<div class="container">
    <div>1</div>
    <div>2</div>
    <div>3</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

有一点你要考虑的是元素的方向定位。对于显示伸缩，有方向属性，即伸缩方向:行或列。在“行”的情况下，元素的放置方向与文本方向相同，而在“列”的情况下，元素从上到下放置。伸缩方向还有 2 个值，分别是“行反转”和“列反转”。当“行反转”应用于弯曲方向时，项目将与文本方向相反放置，而在“列反转”的情况下，项目将从下到上放置。

```
.container{
    display:flex;
    flex-direction:row;
    justify-content:space-around;/*Space around the item in horizontal direction*/
}
.container{
    display:flex;
    flex-direction:column;
    align-item:flex-end;/*Item align at the bottom vertically */
} 
```

Enter fullscreen mode Exit fullscreen mode

当方向反转时，对齐内容和对齐项目的 flex-start 和 flex-end 值也反转，这意味着 flex-start 将开始表现得像 flex-end 一样，反之亦然。

```
.container{
    display:flex;
    flex-direction:row-reverse;
    justify-content:flex-start/*items align to the right side of the container*/
}

.container{
    display:flex;
    flex-direction:column-reverse;
    align-item:flex-end;items align to the top side of the container
} 
```

Enter fullscreen mode Exit fullscreen mode

当项目大小超过容器大小时，是否换行由 flex-wrap 属性决定。flex-wrap 属性将接受以下值。

*   换行:项目换行到新行
*   wrap-reverse:项目以相反的顺序换行
*   nowrap:每个项目都适合一行

参见下面的代码

```
.container{
    display:flex;
    flex-wrap:wrap/*wraps to new line if items are not fitting in one line*/
}
.container{
    display:flex;
    flex-wrap:wrap-reverse/*wraps to new line if items are not fitting in one line in reverse direction*/
}
.container{
    display:flex;
    flex-wrap:nowrap/*Don't wraps to new line even if,items are not fitting in one line*/
} 
```

Enter fullscreen mode Exit fullscreen mode

Flex-box 布局设计将有助于开发者快速高效地设计布局。flex-box 的其他概念将在这篇博文的第 2 部分讨论。