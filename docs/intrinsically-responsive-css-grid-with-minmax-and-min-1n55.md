# 具有 minmax()和 min()的内在响应 CSS 网格

> 原文：<https://dev.to/evanminto/intrinsically-responsive-css-grid-with-minmax-and-min-1n55>

CSS Grid 现在在现代浏览器中得到了广泛的支持，很多人都在用它做着伟大的工作！但不幸的是，该规范最有用的特性之一并不像宣传的那样有效。具体来说，如果不使用媒体查询，就不可能创建一个“本质上响应的网格”——也就是说，**一个基于其容器**大小做出响应的网格。但是由于一些标准现在已经在一些浏览器中可用，并且正在向其他浏览器发展，我们可以解决这个问题！

在我解释解决方案之前，我们先来回顾一下这个问题。使用 CSS Grid，我们可以创建一个以相同大小的列排列的项目网格，并告诉这些列的大小根据可用空间的大小进行调整:

[https://codepen.io/vamptvo/embed/dBawwg?height=600&default-tab=result&embed-version=2](https://codepen.io/vamptvo/embed/dBawwg?height=600&default-tab=result&embed-version=2)

这种技术使用了网格规范中的许多特性:

*   `repeat()`告诉 Grid 创建具有相同大小参数的多个轨迹(不一定是相同的大小，尽管在这种情况下它们最终都是相同的)。

*   `auto-fill`告诉它创建尽可能多的轨道来填充空间。

*   `minmax(10rem, 1fr)`告诉它通过找到最小值`10rem`和最大值`1fr`之间的一个值来确定每个轨迹的大小。`fr`单元确保磁道可以增长以填充任何剩余空间。

由于所有这些，容器在增长时会添加新的列。这个解决方案的一个关键特性是列基于*容器*的大小而改变，而不是视口的大小。这意味着我们可以将它应用于一个可重用的组件，并且确信它看起来总是正确的，而不需要媒体查询来覆盖特定页面上的默认行为。那么问题出在哪里？

## 反应灵敏...有几分

[![Single column of black rectangles. They are overflowing from the right side of the light gray background.](img/e68b21971523117b1857793bef2ad887.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qykc4nd2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpmifenpcd10ov1nx8mh.jpg)

由于每个网格轨迹都有一个最小*的`10rem`的*尺寸，它们不能缩小到那个尺寸以下。这意味着当容器小于`10rem`时，网格项会溢出容器！要解决这个问题，我们必须在媒体查询中包装`grid-template-columns` delcaration，如下所示:

[https://codepen.io/vamptvo/embed/qzvLar?height=600&default-tab=result&embed-version=2](https://codepen.io/vamptvo/embed/qzvLar?height=600&default-tab=result&embed-version=2)

但是现在我们在媒体查询区，我们的网格只在*视窗*很小时切换。我们不能把它放在一个小的*容器*里，否则我们又会遇到溢出的问题。这就是为什么[海登·皮克林](https://twitter.com/heydonworks)和[安迪·贝尔](https://twitter.com/andybelldesign)的 [Every Layout](https://every-layout.dev) 网站一般会避免媒体查询和 CSS 网格，而是通过 Flexbox 实现内在响应。

令人欣慰的是，随着一些新的 CSS 规范开始登陆浏览器，我们可以解决这个问题，让 CSS Grid 充分发挥其作为[内在 web 设计](http://www.zeldman.com/2018/05/02/transcript-intrinsic-web-design-with-jen-simmons-the-big-web-show/)工具的潜力。

## 内在响应式电网

前面的例子遇到了问题，因为我们设置了一个固定值作为最小磁道大小。当容器尺寸缩小时，项目不会随之缩小，因此:溢出。但是，如果我们可以设置一个根据容器大小缩小的最小尺寸，确保我们的轨道对于他们的容器来说不会太大，会怎么样呢？

幸运的是，借助于 [`min()`函数](https://developer.mozilla.org/en-US/docs/Web/CSS/min) :
，我们可以做到这一点

```
ul {
  grid-template-columns: repeat(auto-fill, minmax(min(10rem, 100%), 1fr));
} 
```

`min()`接受一个或多个值并返回最小值。该函数的神奇之处在于，就像`calc()`一样，参数可以使用不同的单位，这允许我们返回基于上下文动态变化的值。在本例中，我们返回容器的当前宽度，最大值为`10rem`。

[![Single column of black rectangles. They are all flush with the edge of the light gray background.](img/389558d4364a6968423f7b34cc615a8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vj5bpbQq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9mrx97jt2722i8z81cjk.jpg)

`min()`是作为 [CSS 值和单位模块级别 4](https://drafts.csswg.org/css-values-4/#comp-func) 的一部分引入的三个新比较功能之一。还有`max()`，它自然与`min()`相反。最后，`clamp()`是一个方便的函数，它将最小值*和最大值*应用于单个值。

在这个例子中，有两种情况我们需要担心，而`min()`解决了这两种情况。当容器的*比`10rem`小*时，我们的`minmax()`表达式中的最小值被设置为容器的全宽，给我们一个没有溢出的漂亮的单列视图。当容器*比`10rem`大*时，最小值被设置为`10rem`，因此我们得到一个响应网格，每个网格至少有`10rem`条相等的轨迹。

## 现实世界中

我知道你在想什么。"这听起来很酷，但实际上我还不能使用它."你只说对了一半。

`min()`没有得到很好的支持，但是它*至少在一个浏览器中是*可用的:Safari 在几个版本之前悄悄地实现了`min()`和`max()`！不幸的是还没有`clamp()`，但是你可以用另外两个来模拟它。另外，Tab Atkins 最近宣布 [Chrome 支持即将到来](https://twitter.com/tabatkins/status/1141830941628780544)。如果你想跟踪浏览器的支持，[can use 有一个页面可以显示所有三个功能](https://caniuse.com/#feat=css-math-functions)。

好消息说完了，下面是上面代码的工作版本(在 Safari 中打开看看它的运行情况):

[https://codepen.io/vamptvo/embed/VJggRL?height=600&default-tab=result&embed-version=2](https://codepen.io/vamptvo/embed/VJggRL?height=600&default-tab=result&embed-version=2)

一个浏览器并不好，但这并不意味着你必须放弃这个解决方案。现在，您可以将它作为一个渐进的增强功能，放在现有的代码中。以下是我的首选解决方案:

[https://codepen.io/vamptvo/embed/bPzzZr?height=600&default-tab=result&embed-version=2](https://codepen.io/vamptvo/embed/bPzzZr?height=600&default-tab=result&embed-version=2)

这个使用了一个`calc()`表达式作为后备。关键部分是`calc(10% + 7.5rem)`，它结合了固定的最小值和百分比宽度。通过基于这两者的*计算最小值，我们降低了触发溢出的阈值。现在，它将在容器略大于`7.5rem`时触发，而在我们之前的解决方案中，它将在`10rem`时触发。这个解决方案有点麻烦，所以你需要调整百分比和固定值来适应你的网格设计。*

以此为后备，我们可以利用 CSS 的能力，通过重新声明`grid-template-columns`来覆盖重复的属性声明。支持`min()`的浏览器，即 Safari，将获得完全响应的解决方案，而 Chrome、Firefox、Edge 和其他浏览器将获得回退。随着越来越多的浏览器支持`min()`，它们会自动切换到新的布局。

## 包装完毕

`min()`、`max()`和`clamp()`函数是非常强大的工具，允许我们根据变化的条件实时切换值。能够在一个真实的浏览器中测试它们打开了许多探索的机会，我相信这个 CSS Grid 的小修补只是开发人员未来使用这些特性的许多方式的皮毛。

* * *

**这篇文章最初发表在[埃文·明托的个人博客](http://evanminto.com/blog/intrinsically-responsive-css-grid-minmax-min/)上。事实证明，如果你想聘请 Evan 从事网站开发、设计或可访问性项目，这是一个不错的选择！**