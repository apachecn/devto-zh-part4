# 顶级 CSS 面试问题及详细答案。✌️🤩✌️第一部分

> 原文：<https://dev.to/theodesp/top-css-interview-questions-with-detailed-answers-part-i-5bjh>

[**阅读原文**](https://codethat.today/tutorial/top-css-interview-questions/part-one/)

CSS 往往会在开发人员中产生过度的头痛，我们并不责怪他们。使用 CSS 是出了名的麻烦和耗时，尤其是当你有详细的设计需求和一个拿着放大镜的老板的时候😎🔎。

就面试而言，我借此机会编制了一份基本能力问题清单，涵盖了 CSS 的基础知识及其解释。这有助于你轻松处理与 CSS 相关的问题，最重要的是理解事情发生的原因。

我首先列出了所有的问题，然后在本文的结尾列出了所有的答案。

这就是了。任何值得尊敬的前端技术人员都应该知道的 CSS 面试问题列表。

## 提问🤔💭

Q1。)**一个链接元素有哪些不同的状态？**

Q2。)**解释 CSS 继承的概念。**

Q3。)**默认情况下，所有属性都被继承。真的还是假的？**

Q4。)**写下一些你能想到的速记属性。**

Q5。)**解释`inherit`和`initial`T3 的区别**

Q6。当两个选择器的特异性水平相同时，最常见的含义是什么？能举个例子吗？

Q7。)**解释什么是`rem`单元，如何工作。**

Q8。)**写下`margin: 1rem 2rem 1rem`的等值。**

Q9。)**用简单的话解释什么是“响应式 CSS”。**

Q10。)**解释相对单位和绝对单位的区别。**

Q11。)**解释什么是`em`单元，如何工作。**

Q12。用户代理或浏览器特有的风格比作者风格更重要。真的还是假的？

Q13。)**包含 ID 选择器是个好习惯吗，为什么？**

Q14。)**我们能覆盖标记为！重要吗？**

Q15。)**关于特异性，通用选择器(*)和组合子(>、+、~)选择器对特异性有影响吗？比如`h1 ~ p`比`p`更具体？**

Q16。就特异性而言，伪类或属性选择器比类选择器更重要、更不重要还是同等重要？

Q17。)**无单位的值和有规定单位的值有什么区别。**

Q18。)**在声明冲突的情况下，在应用样式时，以下哪个优先顺序是正确的，为什么？**

1.  不同的脚本来源或重要性->内联样式->声明的样式顺序
2.  选择器特异性->内联样式->声明的样式顺序
3.  内联样式->声明的样式顺序->选择器特异性

Q19。)**给定下面的片段:**

```
#main-nav a {
  color: white;
  background-color: green;
  padding: 5px;
  text-decoration: none;
}

.featured {
  background-color: orange;        1
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<div id="main-nav">
  <a href="#" class="featured">Bonus</a>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

**我们注意到`featured`链接没有呈现橙色背景。如何重构规则集才能使其工作？**

问题 20。)**给出以下样式:**

```
body {
  font-size: 16px;
}

.block {
  font-size: 1.5em;             
  padding: 1.5em;               
} 
```

Enter fullscreen mode Exit fullscreen mode

**填充块的计算值是多少像素？**

问题 21。)**解释什么是`rem`单元，如何工作。**

问题 22。)**如何在`em`中将根元素字体大小设置为 12px？**

问题 23。)**解释什么是`vmin`和`vmax`单位。**

问题 24。)**解释什么是视口，它包括什么。**

问题 25。)**在元素中设置特定高度属性有什么危险？**

问题 26。)**在默认的装箱模型中，当你定义 2 个元素总共有 100%的宽度时，如果你添加一些填充或者空白会发生什么？是一字排开还是并排显示？(注意组件都是`display: block` )**

Q27。)**怎样才能让基于百分比的身高发挥作用？**

问题 28。)**负边距是如何工作的？**

问题 29。)**列举一些阻止利润崩溃的方法。**

Q30。)**你正试图给一个元素设置一个垂直对齐的属性，但是不起作用。最常见的原因是什么？**

Q31。)**这是利润率崩溃的手段吗？举个简单的例子。**

Q32。)**`box-sizing: border`规则是如何工作的？**

* * *

## Answers⛅🗽

A1。)link 元素中有四种状态:
`unvisited`、`visited`、`hover`和`active`。

A2。)当元素对于给定属性没有级联值时，它可以从祖先元素继承一个值。

例如在下面的钢笔中:

[https://codepen.io/thdespou/embed/ydpeVg?height=600&default-tab=result&embed-version=2](https://codepen.io/thdespou/embed/ydpeVg?height=600&default-tab=result&embed-version=2)

元素继承了样式的属性。

A3。)假的。并非所有属性都是继承的。例如`border`或`will-change`不被继承。

A4。)下面是一些简写属性:

*   `border`是`border-width`、`border-style`和`border-color`的简称

*   `padding`是`padding-top`、`padding-right`、`padding-bottom`和`padding-left`的简称

A5。)有时，如果在同一个元素上既有级联值又有继承值，则默认情况下将应用级联值。如果你不想这样，你可以使用`inherit`来强制继承值。

`initial`将撤消属性的值，使其恢复默认值。

A6。)首先，源顺序决定了将要应用的样式。例如:

```
.nav a {
  color: white;
  background-color: green;
  text-decoration: none;
}

a.featured {                    
  background-color: orange;
} 
```

Enter fullscreen mode Exit fullscreen mode

两者都有相同的特殊性，所以这里的顺序很重要。这意味着，如果我们在一个链接上应用了一个`featured`类，我们
就失去了来自导航元素的`background-color: orange;`规则。

A7。)`rem`指根 em 而不是当前 em。rem 不是相对于当前元素，而是相对于根元素。

A8。)`margin: 1rem 2rem 1rem`等于`margin-top: 1rem`、`margin-left: 2rem`、`margin-right: 2rem`、`margin-bottom: 1rem`，所以中间值对于左右属性是相同的。

A9。)响应式 CSS 是指根据浏览器窗口的分辨率或方向类型(横向或纵向)应用不同规则的样式。例如，它们在手机上看起来不同，在网络上看起来也不同。

A10。)使用`absolute`时，像素等单位的值保持不变。例如，5px 始终是 5px，不会因某些因素而改变。使用`relative`时，单位的值会改变，例如，5em 的含义会根据您使用的元素或属性而改变。

A11。)单元`em`表示当前元素的字体大小，它可以从 html 主体继承。例如，如果我们指定一个字体大小为 16 px 的规则集，那么 1em 的填充将是 16px:

```
.pad-1em {
  font-size: 16px;
  padding: 1em;          1
} 
```

Enter fullscreen mode Exit fullscreen mode

A12。)假的。浏览器样式的特异性最低。如果作者声明了一个样式，它将被应用。

A13。ID 选择器非常具体，它们只适用于一个元素，因此很容易试图用`!important`来克服它们。

A14。)可以，但前提是旁边有同样的规则也是！重要。

```
<p style="color:blue!important;color:red!important;">blue or red?</p> 
```

Enter fullscreen mode Exit fullscreen mode

A15。)都没有。通用选择器(*)和组合符(>、+、~)选择器对特异性没有影响。

A16。)一样。伪类或属性选择器与类选择器具有相同的特性。

A17。)无单位值，如 z-index 或 font-weight，没有单位。在特殊情况下，我们可以使用没有长度单位的值`0`来表示长度 px、em 或 rem。

A18。)正确的是不同的脚本来源或重要性->内联样式->声明的样式顺序

浏览器检查脚本的来源，然后检查内联范围，然后检查特殊性，最后检查声明规则的顺序。

A19。)尽量避免！重要。要么提高`.featured` :
的特异性

```
#main-nav a {
  color: white;
  background-color: green;
  padding: 5px;
  text-decoration: none;
}

#main-nav .featured {
  background-color: orange;        
} 
```

Enter fullscreen mode Exit fullscreen mode

或者更好，降低`a` :
的特异性

```
nav {                               
  margin-top: 10px;
  list-style: none;
  padding-left: 0;
}

.nav li {                            
  display: inline-block;
}

.nav a {                             
  color: white;
  background-color: green;
  padding: 5px;
  text-decoration: none;
}

.nav .featured {                    
  background-color: orange;
} 
```

Enter fullscreen mode Exit fullscreen mode

A20。)是 36px。这是因为填充的值是在计算了字体大小的值之后计算的。

在该示例中，1.5 * 16 = 24px，因此填充将在 24px 的基础上应用 1.5 的乘数，因此它是 1.5 * 24 = 36px。

A21。)`rem`指根 em 而不是当前 em。rem 不是相对于当前元素，而是相对于根元素。

A22。)我们需要添加以下规则:

```
 :root {
  font-size: 0.75em;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者 12/16 = 0.75

A23。)`vmin`是较小尺寸(高度或宽度)的 1/100，`vmax`是较大尺寸的 1/100。它们通常用于使元素始终出现在视口中，即使方向发生变化。

A24。)视口是浏览器窗口中网页可见的区域。这不包括浏览器的地址栏、工具栏和状态栏(如果有的话)。

A25。)如果我们在一个元素中指定了一个高度属性并添加了太多的内容，它将溢出容器并在指定的高度后呈现。

A26。)它们将换行。所以它们会叠在一起，而不是并排。这是因为在默认的装箱模型中，您定义其内容的宽度属性，并且任何填充或边框属性都是累加的。所以最终它们会超过 100%的视口。

A27。)对于基于百分比的高度，我们需要在元素父块上有一个明确定义的高度，因为浏览器需要确定百分比基数。

A28。)通过设置负边距，可以在相应的方向上拉动元素。例如`margin-left: -10px`将元素向左拉 10px。

A29。)我们可以用以下方法停止崩溃:

*   在两个边距之间添加填充或边框属性。
*   将`overflow: auto`添加到容器中。
*   向容器添加固定的内联块或绝对位置。
*   使用 flexbox。
*   使用`table-cell`。

A30。)该元素很可能是基于块的，因为垂直对齐只影响行内和表格单元格元素。

A31。)折叠是指上边距或下边距重叠的情况。当这种情况发生时，就形成单页边空白。例如，一个`p`段落通常有 1em 的上边距和 1em 的下边距。当我们有两个段落在一起时，它们之间产生的边距只有 1m，而不是两个都加上。

当上边距和/或下边距相邻时，它们会重叠在一起，形成一个单独的边距。折叠边距的大小等于最大的边距。

A32。)`box-sizing: border-box`在计算元素的宽度时，将调整框模型以包括填充和边框属性。