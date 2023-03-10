# 顶级 CSS 面试问题及详细答案。👑✨😊🏈⚽🏀🏐⚾第三部分

> 原文：<https://dev.to/theodesp/top-css-interview-questions-with-detailed-answers-part-iii-fea>

[**阅读原文**](https://codethat.today/tutorial/top-css-interview-questions/part-three/)

嘿，你好吗？

这是我们关于 CSS 面试问题系列的第三部分。让我们回顾一下前两部分:

[![theodesp](img/55f62fc8b323734f2864fcb3a0b1da74.png)](/theodesp) [## 顶级 CSS 面试问题及详细答案。✌️🤩✌️第一部分

### theofanis Despoudis 6 月 28 日 197 分钟阅读

#css #interview #career](/theodesp/top-css-interview-questions-with-detailed-answers-part-i-5bjh)[![theodesp](img/55f62fc8b323734f2864fcb3a0b1da74.png)](/theodesp) [## 顶级 CSS 面试问题及详细答案。😁🏆🥇💯第二部分

### theofanis Despoudis 7 月 2 日 198 分钟阅读

#css #interview #career](/theodesp/top-css-interview-questions-with-detailed-answers-part-ii-13l2)

今天，我准备了一套新的常见格式的问题和答案。我们会问一些关于定位、HSL、KSS 和 FOUT 的问题，所以请做好准备。

准备技术面试时的一个好建议。试着把事情写在一张纸上。通常，当我们主要打字和依赖 IDE 时，写作几乎是一项被遗忘的技能，但相当多的公司会给你一张纸或一块白板和一支记号笔来写，所以请确保你对此感到舒适。

让我们开始吧。

## 提问🤔💭

Q1。)**什么是堆叠上下文？**

Q2。)**position 属性的初始值是多少？**

Q3。)**怎样才能让一个元素充满整个视口？**

Q4。)**绝对定位和固定定位的主要区别是什么？**

Q5。当我们将一个相对位置应用于一个元素时，我们总是在页面上看到一个变化。真的还是假的？

Q6。)**有了相对定位，如果我们同时使用`top`和`bottom`规则会发生什么？**

Q7。)**z 索引的目的是什么？**

Q8。)**当浏览器解析 HTML 并呈现 DOM 时，默认情况下如何显示定位元素和非定位元素？**

Q9。)**当一个元素驻留在后面的堆栈上下文中时，该堆栈中的任何其他元素都不能被带到它的后面。真的还是假的？**

Q10。)**作为最佳实践，建议始终创建一个新的堆叠上下文。**

Q11。使用响应式设计，您可以为每个设备保存不同版本的 HTML。真的还是假的？

Q12。)**viewport meta 标签的用途是什么？**

Q13。)**我们如何使用针对`max-width: 25em`或`min-width: 35em`之一的媒体查询？**

Q14。)**你对 CSS 模块了解多少？**

Q15。)**CSS 重置和 CSS 正常化有什么区别？**

Q16。使用 ID 选择器会降低元素的可重用性。真的还是假的？

Q17。)**假设我们有一个如下样式的按钮:**

```
<button class="button">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

```
.button {                            1
  padding: 0.5em 1em;
  border: 1px solid #000;
  border-radius: 0.33em;
  background-color: transparent;
  color: #000;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望有一个类似的风格，但橙色背景色和红色边框的另一个按钮。做那件事的最好方法是什么？

Q18。)**假设我们有下面的 HTML:**

```
<div class="box">
  <img class="box__image" src="image.png">
  <div class="box__body">
    <h3 class="text-center">Title</h3>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.text-center {
  text-align: center:
} 
```

Enter fullscreen mode Exit fullscreen mode

我们希望针对内部的`h3`元素应用一些样式。以下哪一条规则最通用，为什么？

```
.box h3 {}

.box__body > h3 {}

.box__body > .text-center {} 
```

Enter fullscreen mode Exit fullscreen mode

Q19。你熟悉 KSS 吗？关于这件事你能告诉我什么？

问题 20。)**你能列举出`background`规则的所有性质吗？**

问题 21。)**`transform`属性是做什么的？**

问题 22。)**我们不能在一个背景属性中添加两个或多个图像。真的还是假的？**

问题 23。)**以下 hsl 值是什么颜色？**

```
hsl(0, 100%, 100%)
hsl(100, 100%, 100%) 
```

Enter fullscreen mode Exit fullscreen mode

问题 24。)**用 HSL 怎么才能找到一个色相值的补色？**

问题 25。)**字号和行高的主要区别是什么？**

问题 26。)**怎么可能像谷歌字体一样使用外部字体？**

Q27。)**你对无样式文本闪现或 FOUT 这个术语了解多少？**

## Answers⛅🗽

A1。)堆叠上下文是包含一组层的元素。这可以是最初创建的根堆叠上下文。或者它可以是由特定属性和值创建的本地堆栈上下文。这本质上是相对于用户的第三维度。该元素或一组元素由浏览器一起绘制。

A2。)默认是`position: static`。

A3。)通过像这样使用`position: fixed`:

```
.cover {                           
  position: fixed;                          
  top: 0;                                   
  right: 0;                                 
  bottom: 0;                                
  left: 0;                                  
} 
```

Enter fullscreen mode Exit fullscreen mode

A4。)使用固定定位时，实际位置基于视口。使用 absolute 时，其位置基于最近的祖先元素(可能是视口)。

A5。)大多数时候相对位置不会发生视觉上的变化。它将创建一个堆叠上下文。

A6。)底部规则将被忽略。如果我们使用`left`和`right`也会发生同样的情况。`right`将被忽略。

A7。)规则`z-index`决定了元素出现在堆栈上下文中的顺序。堆栈顺序较高的元素总是位于堆栈顺序较低的元素之前。

A8。)真的。同样的事情也发生在位于前端的每个元素上。例如:

```
<div>
  <div class="row relative row-1">
    <div class="absolute">In</div>
  </div>
  <div class="row relative row-2">
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.row {
  height: 100px;
  background-color: blue;
  width: 100px;
  border: 1px solid black;
  z-index:1;
}

.row-1 {
  left: 2em;
}

.row-2 {
  left: 3em;
  top: -4em;
}

.relative {
  position: relative;
}
.absolute {
  position: absolute;
  z-index: 100;
  right: 0;
  bottom: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管我们对`.absolute`元素使用了一个更大的 z 索引，但是根元素的 z 索引是 1，第二行总是在第一行的前面。

A9。)默认情况下，浏览器会将任何已定位的元素放在任何未定位的元素之前。

A10。)假的。堆叠上下文会创建更复杂的布局规则，有时很难确定为什么一些元素会出现在另一些元素之上。因此，我们应该总是有一个好的理由来创建一个堆叠环境。例如，我们应该为门户或模态创建一个，因为我们通常希望将它们作为对话框显示在现有页面的顶部。

A11。)假的。您保持相同的 html，但是您对每个设备维度类使用媒体查询和 CSS 规则，以便容纳内容。

A12。)viewport meta 标签用于指示浏览器如何控制页面的尺寸和缩放。它主要用于在您的页面中启用移动视图，因为没有它，页面看起来会扩大。

A13。)对于 or 运算，我们在:
之间使用逗号(，)

```
@media (max-width: 25em), (min-width: 35em) {} 
```

Enter fullscreen mode Exit fullscreen mode

A14。)CSS 模块是一种命名空间类名和选择器的方法，通过给它们唯一的标识符。这样，我们可以将多种风格分解成自己的组件，最终我们可以将它们组合在一起，而不会产生冲突。

A15。)CSS 重置将对最常见的元素(如 html、body、h1-h6 元素)强制进行无样式布局。等等。以便处理用户代理样式表之间的不一致。它将删除所有默认样式。

另一方面，CSS normalize 试图通过保持它们之间的同步来消除不一致性。它不会删除默认值，但会应用规则使它们保持一致。

A16。)真的。使用类似于`#app .item`的选择器意味着这个模块只能在#app 元素中使用，如果我们想在其他地方使用它，我们将不得不克隆相同的样式副本。

A17。)通过添加修饰符类变体。例如:

```
.button--orange {
  border: 2px solid red;
  background-color: orange;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<button class="button button--orange">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

A18。)第一个太宽泛，因为它针对的是一个`.box`中的所有 h3 元素。第三个有点不相关。文本中心是一个实用程序类。最合适的选择是第二种。

A19。)KSS 代表了 Knyle 样式表。这是一种记录 CSS 和生成样式指南的方法。这个想法是为你的组件编写文档注释，然后用一个工具来解析并为你的项目生成一个样式指南。一个例子是 https://primer.style/components 号。

A20。)`background`是 8 个属性的简写:`background-image`、`background-position`、`background-size`、`background-repeat`、`background-origin`、`background-clip`、`background-attachment`、`background-color`。

A21。)属性允许对元素进行基本的几何变换。比如`scale`、`rotate`或者`translateX`、`translateY`。

A22。)假的。我们可以。使用 CSS 混合模式。只需添加 2 个或更多的图像，并使用`background-blend-mode`规则根据特定的方式混合它们。

A23。)都代表白色。当饱和度和明度为 100%时，它们最大化为白色，与色调值无关。

A24。)通过从色调值中加上或减去 180。互补色位于色轮的反面，使用 HSL 符号计算起来相当容易。

A25。)的`size`代表字体的打印尺寸。`line- height`表示两行不同文本之间内容的总高度。默认情况下，行高比字体大一点(大约大 20%)

A26。)使用`@font-face`规则，我们可以指示浏览器下载并使用自定义字体。

A27。)FOUT 是指当网页试图加载自定义字体，但需要很长时间加载时，浏览器默认字体样式会短暂出现。这会导致注意力分散。使用`font-display`属性，我们可以控制这种行为。

* * *

### 资源延伸阅读

*   [**电网完整指南**](https://css-tricks.com/snippets/css/complete-guide-grid/)

*   [**CSS 定位**](https://css-tricks.com/almanac/properties/p/position/)

*   [**KSS**](https://warpspire.com/kss/)

***   [**-字体显示**](https://css-tricks.com/font-display-masses/)

    *   **