# Flutter:部件树和状态管理

> 原文：<https://dev.to/rubensdemelo/flutter-widget-tree-and-state-management-31an>

### *在旋舞中，一切都是微件*

**窗口小部件树**是一种结构，表示我们的窗口小部件是如何组织的。

网页有一个 **DOM** ，而 Flutter 有一个**窗口小部件树**。

Flutter 还使用了声明式 UI 方法，这意味着界面代表了应用程序的状态。
[![Declarative UI](img/3fc1c3432339660b7aeb28463e2da297.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AX-_ighn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nh74weid5iz9k6vqjwmn.png) 
因此，当我们构建自己的应用程序，彼此组合小部件时，这种结构可以(而且肯定会)增长。随着它变得越来越大，在界面中表示应用程序的状态也变得越来越复杂。

下面的图片来自[文档](https://flutter.dev/docs/development/data-and-backend/state-mgmt/intro)，完美地代表了一辆购物车:
[![Widget Tree](img/8d768c101cae27da03b86fe3765089cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gCy5MuRs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q0m1c1s77u7uky4zwh2m.gif)

*   MyApp -根小部件。我们的应用从这里开始。
*   我的登录屏幕-初始屏幕。
*   MyCatalog -登录后，屏幕上有一个产品列表和一个应用程序栏，允许用户访问购物车。
*   my cart——当用户选择一个产品时，该产品被放入购物车。

这里的关键点是将产品添加到购物车的用户操作，因为您需要通知我的购物车，您的状态已经更改，现在它有一个商品(MyListitem)。

在这个场景中，我们的小部件树很小。想象一下，如果在目录中我们有一个分类和子分类，或者在购物车中我们可以有一个愿望清单，礼物清单，或者用户有一个屏幕，上面有你的个人资料和所有购买历史的详细信息。很容易，我们的部件树会变得更大更复杂。

因此，我们有几个小部件可以使用和/或更新应用程序的状态。

知道了这一点，从现在开始，我们将遵循下面的“规则”:

> 仅当应用程序状态发生变化时，才重新构建所需数量的小部件。
> 如何遵循这个规律？使用一些[状态管理](https://flutter.dev/docs/development/data-and-backend/state-mgmt/options)解决方案。

### **状态管理**

这在任何框架中都不是一个简单的话题。和颤振没有什么不同。

> Flutter 是一个非观点化的框架，也就是说，它不“思考”你应该如何做事情。它不会强迫您使用嵌入式解决方案和/或模式。它让您可以自由选择最适合您项目的解决方案。

本叔叔已经教过我们了:

> 权力越大，责任越大

为您的项目选择正确的解决方案是良好开发周期的基础。你不可能提前发现所选择的解决方案已经成为一个瓶颈，并使其难以维护，创建新功能，甚至限制我们的应用程序的扩展。

#### **每个问题都没有更好的解决方案或解决方案。他们每个人都有自己的长处和短处。**

在下一篇文章中，我将写一些关于 scoped_model 和 provider 的内容。

**[scoped_model](https://github.com/brianegan/scoped_model)** 是 Flutter 文档明确推荐的第一个包。

**[提供者](https://github.com/rrousselGit/provider)** 是最新的推荐。Flutter 团队在最后的 Google IO 上支持使用这个包。它比 scoped_model 更健壮，并且仍然易于理解。

一会儿见:)