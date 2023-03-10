# 顶级 CSS 面试问题及详细答案。😁🏆🥇💯第二部分

> 原文：<https://dev.to/theodesp/top-css-interview-questions-with-detailed-answers-part-ii-13l2>

[**阅读原文**](https://codethat.today/tutorial/top-css-interview-questions/part-two/)

嘿欢迎回来。这是关于顶级 CSS 面试问答系列的第二篇文章。我希望你喜欢第一部分:

[![theodesp](img/55f62fc8b323734f2864fcb3a0b1da74.png)](/theodesp) [## 顶级 CSS 面试问题及详细答案。✌️🤩✌️第一部分

### theofanis Despoudis 6 月 28 日 197 分钟阅读

#css #interview #career](/theodesp/top-css-interview-questions-with-detailed-answers-part-i-5bjh)

今天我将发布一个全新的问题列表，关于 CSS flex-box，网格布局和其他。一如既往，我会先发布问题，然后是答案。

我希望答案是有代表性的，但不是绝对的要求。如果你正在准备一个技术面试，你需要能够用你自己的话解释事情是如何运作的，也许可以写下你自己的例子。没有必要什么都知道，但至少要承认你知道什么或不知道什么，这样你才能说清楚。

那我们开始吧。

## 提问🤔💭

Q1。)**如果把`float`加到同方向的兄弟元素会怎么样？**

Q2。)**浮动元素增加了其父元素的高度。对还是错？**

Q3。)**什么是伪元素？举个例子。**

Q4。)**我们通常如何将页面内容居中？**

Q5。)**用简单的语言解释什么是块格式化上下文或 BFC？**

Q6。)**解释一下`:nth-child`和`:nth-of-type`的区别？**

Q7。)**flex 容器的默认方向是什么？**

Q8。)**展示我们如何使用属性选择器，根据元素的某些属性来定位元素。**

Q9。)**一个 flex-box 布局包含几个轴？**

Q10。)**`inline-flex`和`inline-block`有什么区别？**

Q11。)**如果一个项目的 flex-grow 为 0 会发生什么？**

Q12。)**给出下面的 HTML 和 CSS:**

```
<div class="flex">
  <div class="column"></div>
  <div class="column"></div>
  <div class="column"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.flex {
  display: flex;
}
.column {
  flex-basis: 40%;
  flex-shrink: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们的 flex 容器会发生什么？怎么才能修好？

Q13。)**什么是规则的全展开`flex: 2`？**

Q14。)**给出下面的 HTML 和 CSS:**

```
<body class="flex">
  <div class="column-main"></div>
  <div class="column-sidebar"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.flex {
  display: flex;
}
.column-main {
  flex: 2;
}

.column-sidebar {
  flex: 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还知道`body`元素没有边距或填充，并且视窗的总宽度是 1157 像素。

`column-main`和`column-sidebar`元素的像素宽度是多少？

Q15。)**给出以下 HTML:**

```
<ul class="nav">
  <li><a href="/">Home</a></li>
  <li><a href="/blog">Blog</a></li>
  <li><a href="/about">About</a></li>
  <li class="nav-right"><a href="/contact">Contact</a></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

我们如何定位跟在另一个列表项后面的每个列表项？

Q16。)**如果在 Safari 中运行以下规则会怎么样？**

```
.nav {
  display: -ms-flexbox;
} 
```

Enter fullscreen mode Exit fullscreen mode

Q17。)**当我们有`flex-direction: column`的时候，`flex-basis`、`flex-grow`和`flex-shrink`申请高度还是宽度？**

Q18。)**我们想要选择所有非单选或数字类型的输入元素。我们如何做到这一点？**

Q19。)**flex-box 容器中的`align-items`和`align-content`有什么区别？**

问题 20。)**你有一个`input type=text`元素，你应用一个`display: block`规则。您会注意到输入位于自己的行中，但不会扩展到填满输入文本区域的整个宽度。这是为什么呢？你能如何修理它？**

问题 21。)**网格布局中的`fr`单元是什么意思？**

问题 22。)**当`flex-wrap`是`wrap`或`wrap-reverse`时，那么`flex-shrink`规则会发生什么变化？**

问题 23。)**我们有下面的 html 和 css:**

```
<div class="flex">
  <div class="column"></div>
  <div class="column"></div>
  <div class="column"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.flex {
  display: flex;
  justify-content: flex-start;
}
.column {
  background-color: blue;
  width: 70px;
  margin: auto;
  height: 70px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们注意到，根据`justify-content`规则，项目没有向左对齐。这是为什么呢？

问题 24。)**怎样才能创建一个等分数的 3x2 网格布局，单元格之间的间距为 0.25em？**

问题 25。)**随着网格布局的出现，flex-box 就没用了。真的还是假的？**

问题 26。)**我们需要定义一个两列网格，用三条垂直线分别命名为起点、中间和终点。第一列的大小是第二列的两倍。我们如何做到这一点？**

Q27。)**你对功能查询了解多少？我们如何声明它们？**

问题 28。)**`object-fit`规则的目的是什么？**

问题 29。)**定义网格模板时，`auto-fill`和`auto-fit`属性有什么区别？**

Q30。) **`justify-content`在网格布局内部没有实际意义。真的还是假的？**

## Answers⛅🗽

A1。)如果你在同一个方向浮动多个元素，它们会并排堆叠。

A2。)假的。这是因为浮动元素只允许文本环绕它们。它不会使父容器变大以适应新的高度。

A3。)伪元素是以双冒号(::)语法开头的特殊选择器。它们通常针对文档的特定部分。例如，`::before`和`::after`用于在元素的开头或结尾插入内容。

A4。)我们使用两个嵌套的容器，然后在内部容器上设置边距，将其放置在外部容器中。例如:

```
.container {
  max-width: 960px;
  margin: 0 auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<body class="main">
  <div class="container"></div>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

这样，在较小的视窗中，内部容器将充满屏幕，但在较大的视窗中，它将在 960 像素宽度处停止。

A5。)块格式化环境是一个区域，块盒的布局出现在这个区域中，并且在这个区域中浮动与其他元素交互。

换句话说，我们有一个部分，其中一些规则仅适用于相同块格式上下文中的内容，而不适用于不同块格式上下文之间的内容。创建 BFC 的一个简单方法是使用块元素，其中 overflow 的值不是 visible `overflow: auto`或者使用绝对定位元素。

A6。)我们使用`:nth-child`在从 0 开始的兄弟元素中定位一个元素位置。例如:n-child(0)将针对第一个孩子，`:nth-child(2n)`将针对每第二个孩子，以此类推。我们使用`:nth-of-type`来定位一个元素在它的兄弟元素中的位置，从 0 开始，但也是相同的类型。

A7。)默认情况下，伸缩项从左到右并排排列成一行。

A8。)属性选择器是这样定义的:

```
[class*="column-"] {
} 
```

Enter fullscreen mode Exit fullscreen mode

这将匹配其 class 属性包含`column-`值的所有元素。比如:
、`column-2`会匹配而`column`不会。

A9。)两轴。主轴和横轴。

A10。)一个`inline-flex`创建一个容器，其中的元素
不会自动增长到 100%宽度。它使 flex 容器内联显示，容器项目的行为与`display:flex`相同。

使用`inline-block`时，项目的行为与`display:block`相同，容器被内联显示。

A11。)当项目的弹性增长为 0 时，它不会增长超过其弹性基础参数。

A12。)由于`flex-shrink: 0`规则，flex 项目将溢出容器，因为它们加起来超过宽度的 100%。我们只要把它改成`flex-shrink: 1`就可以修复了。

A13。)`flex: 2`是:
的快捷方式

```
flex-grow: 2;
flex-shrink: 1;
flex-basis: 0%; 
```

Enter fullscreen mode Exit fullscreen mode

A14。)将分别约为`771px`和`386px`。我们可以将其计算为总宽度的比率，例如，我们有总伸缩项= 2 + 1 = 3，`column-main`占用 2/3 = 0.666 乘以 1157 ~ 771px，其余为`column-sidebar` ~ 386px。

A15。)我们需要使用下面的选择器:

```
.nav > li + li {} 
```

Enter fullscreen mode Exit fullscreen mode

`nav > li`将指向每个`.nav`容器的第一个列表项，而`+ li`将匹配紧随其后的元素。

A16。)它会忽略它，并且不会应用`display: flex`，因为 Safari 没有相关的规则。这是一个在 IE10 等旧浏览器中工作的供应商前缀的例子。

A17。)它将应用于元素的高度，而不是宽度。

A18。)我们需要使用两个`not`选择器来反转选择并将它们组合在一起。比如:

```
input:not([type=radio]):not([type=number] 
```

Enter fullscreen mode Exit fullscreen mode

A19。)规则`align-items`控制项目沿横轴的定位。例如，对于`flex-direction: row`，它将用于列轴，反之亦然。`align-content`仅在`flex-wrap`启用时适用，它控制沿横轴的柔性行的间距。

A20。)对于`input`元素来说，`size`属性决定了它的宽度，这大致表示了在不滚动的情况下它应该包含的字符数。设置`display:block will`只是将它放置在自己的行中。您可以通过应用`width: 100%`规则来修复它。

A21。)`fr`表示在网格布局中使用的分数单位，以分数的形式表示整体的一部分。

A22。)在这种情况下，因为我们允许换行，`flex-shrink`将不会根据该规则收缩。

A23。)因为我们添加了`margin: auto;`，`justify-content`规则将没有效果。

A24。)我们可以使用下面的 css:

```
.grid {
  display: grid;                           
  grid-template-columns: 1fr 1fr 1fr;      
  grid-template-rows: 1fr 1fr;             
  grid-gap: 0.25em;                         
} 
```

Enter fullscreen mode Exit fullscreen mode

A25。)假的。网格布局和柔性盒是互补的。例如，Flex-box 是一维的，适合基于内容的规则，而 grid 是二维的，适合基于布局的规则。

A26。)我们需要使用下面的规则:

```
grid-template-columns: [start] 2fr [middle] 1fr [end]; 
```

Enter fullscreen mode Exit fullscreen mode

这里`[start]`标记了起始网格线`[middle]`中间的一条和`[end]`最后的一条。中间的分数代表布局的跨度。我们现在可以用它来举例:

```
grid-column: [start] / [end]; 
```

Enter fullscreen mode Exit fullscreen mode

并且该柱将从头到尾横跨整个宽度。

A27。)功能查询很像功能标志。它们是我们为当前浏览器支持的特定 css 属性或值 validate 添加的检查。例如:

```
@supports (display: grid) {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

将检查是否支持`display: grid`规则，然后将阻止规则应用于当前样式。否则，该样式将被跳过。

A28。)规则只对`img`或`video`元素有效。它用于确定如何调整它们的大小以适合它的容器。
例如，我们有一个自然长宽比为 4/3 的图像:

```
img {
  width: 400px;
  height: 300px;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们改变图像尺寸为`width: 300px, height 400px`，它将打破其长宽比，看起来扭曲。
为了解决这个问题，我们需要应用例如`object-fit: cover`或`object-fit:contain`来保持纵横比不变。

A29。)使用`auto-fill`,浏览器将尝试在同一行或同一列中放置尽可能多的项目，如果有可用空间，将创建额外的空轨道。另一方面，`auto-fit`不会创建额外的轨道，但它会尝试将轨道扩展到，以便它们占用任何可用空间。例如以下规则:

```
.grid-container--fill {
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
}

.grid-container--fit {
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
} 
```

Enter fullscreen mode Exit fullscreen mode

用`grid-container--fill`添加的列越多，它会将项目压缩到最小 100 像素的宽度，一旦达到这个宽度，它就会换行到下一行。使用`grid-container--fit`，它将尝试将它们的可用空间扩展到可用片段宽度。

A30。)假的。确实如此。它在 grid 容器中用于水平放置轨迹。
例如:

```
<div class="grid">
  <div>1</div>
  <div>2</div>
  <div>3</div>
  <div>4</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.grid {
  display: grid;
  grid-template-columns: repeat(4, 200px);
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果容器空间大于 800px 宽度，会将所有列居中放置。

* * *

### 资源延伸阅读

*   [**电网完整指南**](https://css-tricks.com/snippets/css/complete-guide-grid/)

*   [**Flexbox 完全指南**](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

*   [**关于彩车的一切**](https://css-tricks.com/all-about-floats/)

*   