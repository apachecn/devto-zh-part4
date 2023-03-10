# 布拉佐尔系列

> 原文：<https://dev.to/joshrkulp/blazor-series-3caj>

在接下来的几篇文章中，我将概述我在编写第一个 Blazor 应用程序时学到的知识。Blazor 是微软的一项新技术，通过 WASM 在浏览器中运行 C#。如果你想了解更多的基础知识，请去 https://Blazor.net

站点漫游共享 C#
传递参数
从 C#
调试
发布到 Github 页面
(我会在发布时添加链接)

在这篇文章中，我将做一个简短的站点浏览，给你一些我们将要看的代码的背景。

先说 app 的架构。我从“blazor(ASP.NET 核心托管)”的基本 Blazor 模板开始
[![](img/3dcb042ffbb488449bafd926b864a299.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7uRondID--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/266dcrishg4t4snojpba.png)

因此，我们有一个服务器项目，客户端项目，共享项目。

*   服务器项目-这将是你的 c#后端，我们不会在这个项目中使用它，因为我们希望它从没有后端的客户端运行(有点，我使用 airtable 作为一个便宜的数据库)

*   客户端项目——这是你的站点所在的地方，也是我们用 blazor 创建组件的地方。

*   Sharred 项目——这是您可以在其他 C#项目之间共享的类和模型所在的位置

[![](img/8408b5d320bb578f97cb5ae80c6f265b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D7C0_3od--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z6r2ta160olc69szt1b4.PNG)

接下来，我们来看看 app 本身。这是一个基本的玉米卷订购应用程序，构建为一个单页应用程序。

# 列表页面

给你一份玉米卷的清单和价格。
[![](img/2416dd2463a687af6c14cfb64078292a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--JzzGgEf6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v4uwcx2saki31y9mpbx3.png)

# 配置弹出

以免你在你的玉米卷里加了额外的东西，然后把它放入你的购物车
[![](img/c0fa5204b36285f03e6409a437d3730b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jF7BgJcA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1vjzg1n7o4wyg4fwzoku.png)

# 推车&结账

-跟踪您购物车中的玉米卷
-您购物车中所有商品的 JavaScript 提醒。
[![](img/e7675e8565de8f785ef06f4e2000b254.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SMDp7UnP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3vm5rldjsomiwtrfz2x.png)

谢谢你检查这个。你用过 blazor 吗？你是否认为现在开始考虑 blazor 还为时过早，因为它还没有准备好生产？