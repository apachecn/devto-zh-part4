# 使用 hyperHTML-10 的简单应用程序，第三方库

> 原文：<https://dev.to/pinguxx/easy-apps-with-hyperhtml-10-3rd-party-libraries-2n7b>

第十部分作者

[![pinguxx image](img/b3fd6923e5679952858c6b2c43518de0.png)](/pinguxx)

## [伊凡](/pinguxx)

[standard geek](/pinguxx)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)pinguxx](https://twitter.com/pinguxx)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)pinguxx](https://github.com/pinguxx)

[![paritho image](img/31ca3c53383636df8ffd826c26771850.png)](/paritho)

## [保罗·汤普森](/paritho)

[lover of dogs and javascript. and coffee. and writing.](/paritho)

[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)帕里托](https://github.com/paritho)

1.  [介绍，焊线/绑定](https://dev.to/pinguxx/easy-apps-with-hyperhtml-1-31cc)
2.  [事件和组件](https://dev.to/pinguxx/easy-apps-with-hyperhtml-2-hbi)
3.  [关于组件和简单状态管理的 Moar】](https://dev.to/pinguxx/easy-apps-with-hyperhtml-3-1m3l)
4.  [电线类型和自定义定义(意图)](https://dev.to/pinguxx/easy-apps-with-hyperhtml-4-38k3)
5.  [用 hyper 定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-5-5hdl-temp-slug-7285698)
6.  [定制我的定制元素](https://dev.to/pinguxx/easy-apps-with-hyperhtml-6-h5b-temp-slug-4579110)
7.  [测试！](https://dev.to/pinguxx/easy-apps-with-hyperhtml-7-12g4-temp-slug-5472234)
8.  [异步加载、占位符和带超链接的提前输入](https://dev.to/pinguxx/easy-apps-with-hyperhtml-8-async-loading-5d96)
9.  [搬运路线](https://dev.to/pinguxx/easy-apps-with-hyperhtml-9-routing-59k0)
10.  **第三方库**

<figure>

[![](img/ab16d4d3525b34957c4575ad984ecb9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CE7gVdZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q1bejwaip8ahozvruzh6.jpeg)

<figcaption>Photo by [Markus Spiske](https://unsplash.com/photos/C0koz3G1I4I?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/block?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

将第三方库与您的框架相结合通常会很有挑战性。因为 hyperHTML 是基于 JavaScript 标准构建的，所以将它与其他库一起使用通常不会有任何痛苦，也很容易。这是 hyper 的主要优点之一:它与他人合作愉快。

在第 4 部分中，我们看到了如何定义和使用意图。在第 2 部分中，我们使用了组件，在第 5 部分中，我们研究了定制元素。

> 如果你错过了其中的任何一部分，现在就回去复习一下。我们将利用在那里学到的知识。

今天，我们将使用第三方库来丰富这三者。只需加载和使用。

### 时间📅和意图😉

首先，我们将使用 [dayjs](https://github.com/iamkun/dayjs) 。Dayjs 是 [moment.js](https://momentjs.com/) 的一个更简单更小的替代方案。如果您正在寻找一个占用空间较小的 datetime 库，一个熟悉的逻辑 API，dayjs 是一个很好的选择。在第 4 部分中，我们有一个日期格式意图，即以特定格式打印日期。它看起来像这样: