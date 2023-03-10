# 从头开始构建 UI，响应内容

> 原文：<https://dev.to/llorentegerman/building-a-ui-from-scratch-responsive-content-6b9>

这是`Building a UI from scratch`系列的第三篇文章:

*   第 1 部分:[基于 ReactJS](https://dev.to/llorentegerman/building-a-ui-from-scratch-based-on-a-design-with-reactjs-3l1e) 的设计，从头开始构建 UI。
*   第 2 部分:[从头开始构建 UI，响应式侧边栏和标题](https://dev.to/llorentegerman/building-a-ui-from-scratch-responsive-sidebar-and-header-443g)。
*   第 3 部分:[从头开始构建 UI，响应内容](https://dev.to/llorentegerman/building-a-ui-from-scratch-responsive-content-6b9)。

**现场演示**:【https://llorentegerman.github.io/react-admin-dashboard/ T2】

**储存库**:【https://github.com/llorentegerman/react-admin-dashboard】T2

## 响应内容

作为第三篇文章的一部分，我们将看到如何制作出现在设计中的内容([参见第一部分](https://dev.to/llorentegerman/building-a-ui-from-scratch-based-on-a-design-with-reactjs-3l1e))。
我们想让它响应迅速，我们想让它看起来像这样:
[![](img/9713324a4658689339d54f0e50050dac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O2fpSfJM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://g.recordit.co/cWkPDhuw0P.gif)

正如我在上一篇文章中所说，我们没有一个响应式的设计可以遵循，所以我们将保持简单，一个位于 **768px** 的主断点。
我们可以在内容中识别出 3 个主要部分:

```
1- Row of MiniCards
2- Today's trends (graph + stats)
3- Row with 2 cards:
    4- Unresolved tickets
    5- Tasks 
```

[![](img/518794837b62b39ff12e964a43456c0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dk6kzq1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/9fTPpZLv/content1.png)

#### MiniCardComponent.js

这是一个简单的组件，只是一个带有`title`和`value`的列。该列的内容必须居中。