# 项目输入，列输出|使用 CSS 网格

> 原文：<https://dev.to/mikister/items-in-columns-out-playing-with-css-grid-1en8>

## [T1】简介](#intro)

我正在开始一个关于 css grid 的系列文章，从一些简单的事情开始。

## 想法

只需将项目添加到某个元素中，并使用 CSS Grid 将它们自动排序到列(或行)中。

## 演示

[https://codepen.io/mikister/embed/VwZVGXB?height=600&default-tab=css,result&embed-version=2](https://codepen.io/mikister/embed/VwZVGXB?height=600&default-tab=css,result&embed-version=2)

## 工作原理

实际上，我们只需要三个属性就可以做到这一点(演示中的其他内容只是为了看起来)。他们是:

```
.parent_element {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  grid-auto-rows: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以**的第一个属性**告诉浏览器，具有该类的元素应该表现得像一个网格。

第二个属性告诉我们应该有多少列。
`repeat(7, 1fr)`只是`1fr 1fr 1fr 1fr 1fr 1fr 1fr`的语法糖。
`fr`是一个表示分数的 css 单位，所以它将元素分成 7 个相等的列。

第三个属性告诉浏览器如何设置行，传递给它的值是行高。

如果你想把事情按**行**排序，那么只需使用:

```
.parent_element {
  display: grid;
  grid-template-rows: repeat(7, 1fr);
  grid-auto-columns: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用例

当所有元素**都相同**(或至少相似)**高度**时，你会用到这种方法。以下是一些有用的想法:

*   **网上商店商品**，当你有一张包含商品图片、名称和其他属性的卡片时，你可以将它们组织成一个整齐的网格
*   **组合项目**，同样是卡片，概念同上
*   如果你需要在网站上放一个日历的话，可以放一个日历
*   **图库**，仅当所有图片高度相同时
*   Dropbox/Google Drive 上的网格视图 -也许你决定要为你的投资组合从头开始构建它们

## 不要用这个做什么

*   **砖石布局**(想想 pintrest 卡)，为什么，因为每一行的高度与其最高的成员相同，并且到处都是奇怪的空白，而不是使用`columns`属性或多个垂直 flexboxes 并在它们之间划分元素

* * *

如果你有任何建议，请在评论中告诉我。