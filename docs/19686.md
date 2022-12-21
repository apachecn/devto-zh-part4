# 回复:创作-中间-Google.com

> 原文：<https://dev.to/dys/re-creation-intermediate-google-com-349n>

## 从基础开始重述

在重建 Google.com 的基础上，我们分三步走:

1.  将内容转换为纯文本
    *   我们将内容清单仅限于第一级分组，而没有深入挖掘子组
    *   我们没有费心通过交互来展示其他内容和用户界面
2.  在 HTML 中标记纯文本
    *   我们使用简单的 HTML 元素，如
    *   我们通过数据 URI(而不是绝对 URL)使用 SVG 我们实际上可以使用任何图像类型，如 PNG 或 JPG——我们只需对其进行编码
3.  CSS 中 HTML 元素的样式
    *   我们将 CSS 属性的使用限制在以下范围内:填充、边距、宽度、高度、背景色、颜色
    *   字体属性保留为默认的浏览器样式
    *   涉及交互的样式，比如悬停，还没有解决

结果是一个相当“看起来不完整”的网页。[亲眼看看](https://codepen.io/briandys/pen/eaaKyq):

[![](img/9a48bd4ca78d08768796b4aa373fde30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DjCkpPzk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w5y6uurobkf35v22pwoo.png)

尽管如此，对于一个刚刚开始学习 HTML 和 CSS 知识的人来说，这并不坏——这就是为什么我们把它推到中级水平！

* * *

## 准备好了吗？我们开始吧！

再现 Google.com 基本上有三个步骤:

1.  将内容转换为纯文本
2.  在 HTML 中标记纯文本
3.  CSS 中 HTML 元素的样式

这是我们正在重建的模型:

[![](img/8fa58b7ccb4fcb925fa196e963bd4d39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5cJXPrZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yfn1orsd33xw3yppxhnf.png)

每一步有 3 个回合，所以我在 CodePen 中一共做了 9 个例子:

## 第一步:纯文本

### 第一步，第一轮:内容盘点

[https://codepen.io/briandys/embed/JqQpMo?height=600&default-tab=html,result&embed-version=2](https://codepen.io/briandys/embed/JqQpMo?height=600&default-tab=html,result&embed-version=2)

### 第一步，第二轮:分组和贴标签

[https://codepen.io/briandys/embed/jojxyM?height=600&default-tab=html,result&embed-version=2](https://codepen.io/briandys/embed/jojxyM?height=600&default-tab=html,result&embed-version=2)

### 第 1 步，第 3 轮:结构

[https://codepen.io/briandys/embed/rgEKgB?height=600&default-tab=html,result&embed-version=2](https://codepen.io/briandys/embed/rgEKgB?height=600&default-tab=html,result&embed-version=2)

## 第二步:HTML

### 第二步，第一轮:结构标记

[https://codepen.io/briandys/embed/vwqzro?height=600&default-tab=html,result&embed-version=2](https://codepen.io/briandys/embed/vwqzro?height=600&default-tab=html,result&embed-version=2)

### 第二步，第二轮:分组标记

[https://codepen.io/briandys/embed/ZNdqER?height=600&default-tab=html,result&embed-version=2](https://codepen.io/briandys/embed/ZNdqER?height=600&default-tab=html,result&embed-version=2)

### 第 2 步，第 3 轮:单独标记

[https://codepen.io/briandys/embed/XwLxOr?height=600&default-tab=html,result&embed-version=2](https://codepen.io/briandys/embed/XwLxOr?height=600&default-tab=html,result&embed-version=2)

## 第三步:CSS

### 第三步，第一轮:个人风格

[https://codepen.io/briandys/embed/ZNdddp?height=600&default-tab=css,result&embed-version=2](https://codepen.io/briandys/embed/ZNdddp?height=600&default-tab=css,result&embed-version=2)

### 第 3 步，第 2 轮:团体风格

[https://codepen.io/briandys/embed/ZNgBYo?height=600&default-tab=css,result&embed-version=2](https://codepen.io/briandys/embed/ZNgBYo?height=600&default-tab=css,result&embed-version=2)

### 第 3 步，第 3 轮:结构样式

[https://codepen.io/briandys/embed/VOopWV?height=600&default-tab=css,result&embed-version=2](https://codepen.io/briandys/embed/VOopWV?height=600&default-tab=css,result&embed-version=2)

* * *

这是我们活动最终状态的截图:

[![](img/d42c4dd3438974c79bf91e972c5491cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s----ROEPku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vgmwy0qaaycv0z0cjn6u.png)

同样，仍然有很大的改进空间，我们将在高级水平上解决这些问题。

* * *

## 接下来是什么？

我们可能会在下一篇文章中直接进入高级水平。

感谢阅读！

布莱恩·dys