# 使用 hyperHTML-9 的简单应用程序，路由

> 原文：<https://dev.to/pinguxx/easy-apps-with-hyperhtml-9-routing-59k0>

第 9 部分作者

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
9.  **搬运路线**
10.  [第三方库](https://dev.to/pinguxx/easy-apps-with-hyperhtml-10-3rd-party-libraries-2n7b)

[![](img/60e8aebeb7ad869a1d600ca497226067.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l_Nl-gt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AadsYsCwwYGzqLIhnP5AD6Q.jpeg)

如果您错过了前面的任何部分，现在是补上您错过的部分的好时机。具体来说，第 9 部分建立在第 8 部分的基础上。回想一下，我们在第 8 部分中构建了一个表，并用来自 cryptocompare api 的真实数据填充了它。现在我们正在检索真实数据，我们可以进一步增强我们的应用程序。这个应用程序是路由的一个很好的候选。通过使用 routes，我们可以将它变成一个单页面应用程序或 SPA。每次我们想要显示应用程序的不同部分时，spa 不需要重新加载整个页面。

这是我们目前的应用程序:

[https://stackblitz.com/edit/advanced-app-03?embed=1&&](https://stackblitz.com/edit/advanced-app-03?embed=1&&)

我们将添加一个新选项卡来显示下拉列表中所选硬币的历史数据。为此，我们将使用状态管理、我们的元素和路由管理。

* * *

### 重组

到目前为止，我们只在同一个页面上使用了一些元素。虽然这很容易管理，但我们现在需要更好的组织来处理路线和不同的元素一起工作。

[![](img/23736490282c246f39068379c880aa08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CIG-CLWA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/205/1%2AS1ik3vMIJxSjvLrqrKBfjw.png)

我们将创建一个名为 elements 的文件夹，并将已经创建的文件夹移入其中。意图也一样。不要忘记:因为我们改变了路径，所以我们需要更新所有路径的导入语句。

此外，让我们创建一个组件文件夹。我们很快就会用到它。

这是我们现在的准则

[https://stackblitz.com/edit/advanced-app-04?embed=1&&](https://stackblitz.com/edit/advanced-app-04?embed=1&&)

* * *

### 路由

有大量不同的路由选择，很可能你最喜欢的框架就有一个 hyper 也有。是的，这是 YAAT——又一个安德里亚工具。[https://github.com/WebReflection/hyperhtml-app](https://github.com/WebReflection/hyperhtml-app)。你不需要使用 hyper 来使用这个库，它在你如何定义路线上类似于 Express 框架。

* * *

#### 路线类型

*   **get("/path/:param "，cb)** :对指定路径的 get 请求将被捕获。在回调 cb 中，param 将作为第一个参数的属性。例如:ctx.param
*   **use(paths，cb)** :将中间件添加到指定的路径中。这类似于 get，但是它接受一个路径数组
*   **delete(path，cb)** :从路径中删除回调
*   **param(name，cb)** :允许您指定在传递参数名称时使用一次的函数
*   **导航(路径)**:以编程方式导航至路径

对于我们当前的项目，我们将主要使用 get，但是正如你所看到的，hyperhtml-app 有许多选项供你使用。

首先，让我们导入库并创建几个路由。因为我们将添加一个索引路径，所以我们可以去掉 index.html 和 index.js 文件中的代码。我们将用这个替换它:

新 index.html