# 用 Redux 思考(当你只知道 MVC 的时候)

> 原文：<https://dev.to/mlevkov/thinking-in-redux-when-all-you-ve-known-is-mvc-4f16>

当推出一个移动应用程序时，你通常会做出的第一个决定是:我们使用什么语言？经过一番考虑，当你意识到你不想学习 Kotlin 和 Swift 时，你最终选择了 React-Native。从我自己的经验来看，学习一门新的“语言”或框架并不是一个大问题，但是 man oh man，确实做出了反应——native 和 redux 给了我一段艰难的时间。本文没有解释 React-Native 是如何工作的(因为这不是最难的部分)。接下来几段的目的是帮助任何人阅读从“用 MVC 思考”到“用 Redux 思考”的转变。希望有帮助。

## React-Native 和 Redux？

一旦你开始学习 react-native(或 react ),在有人提到 redux 之前，你大概有 3 个栈溢出问题或中等帖子。你太开心了。你开始了解状态和道具，你知道 componentDidMount 是做什么的，你甚至知道如何正确地创建你的组件，使它们可重用。现在你突然发现自己在 egghead.io 上，某个家伙正在谈论商店、reducer 合成、动作和将状态映射到道具。

[![confused](img/85236250d207eaa900128bfa200782dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8uHdBE7A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://miro.medium.com/max/370/1%2AZkpuItpBQjkDu1mXDbR35A.gif)

你也意识到，虽然在你能够做到之前: **$(”。我的按钮”)。单击()；**获取按钮做某事；现在已经过了大约 3 个小时，你的一个按钮没有任何作用。

## 有些类比

如果你来自 MVC(或 MVVC)世界，你会习惯模型、视图和控制器(咄)。然而在 [Redux](https://redux.js.org/introduction/getting-started) 中，我们处理的是动作、reducers、存储和组件。试图将 MVC“翻译”成 Redux 是一件棘手的事情，但我会这样做:

**动作=控制器。**把你的行动当成控制者。每当你想在你的应用程序中发生一些事情(例如，加载一些数据，改变一个 isLoading 标志从真到假…)你将不得不分派一个动作。就像在 MVC 中，你必须调用一个控制器端点。

**减速器=型号。**算是吧。您的 reducers 将负责保存您的应用程序的当前状态(即用户信息、从 api 加载的信息、您想要显示的项目……)。它也是决定当一个动作被调用时做什么的部分。在 MVC 中，你可能有一个带有方法 setName()的模型，而在 Redux 中，你将让一个 reducer 处理一个动作来设置状态中的名称。

**店铺=？？？。**商店是 Redux 特有的，在 MVC 中没有真正的对等物。不过不用担心。这部分是幕后处理的。存储就像一个状态容器，它聚集了所有的 reducers。它有一个获取当前状态的方法，并公开了订阅状态更改的方法(使用“connect()”方法)。这将允许你调用动作并把它们作为道具传递给你的组件。

**组件=视图。**组件有点像您的智能视图。他们展示从州政府获得的信息。我建议把你的组件分成两部分。一个仅用于表示部分(哑组件)，一个用于处理所有的动作和状态变化(智能组件)。

### 我一直在发关于代码的微博。如果你有关于如何提高你的开发技能的问题，给我一个关注

## 从 MVC 思维走向 Redux 思维

[![Typical MVC. When life was easy.](img/f84ce2d5e1633d0476a94ce15d71ace6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aqNzUboK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/670/1%2AxORdWwOFLR-6D4ghvUa6AA.png)

正如您在上面的图表中所看到的(并且从经验中知道),数据可以以两种方式流动。您在视图中按下一个按钮，它会向控制器发送一条消息并更新模型。模型更改一些值，将该值返回给控制器，控制器刷新视图。放松点，皮兹！

[![Redux flow. Life sucks now](img/0c71f1f4b72c812966c09a21238c0dac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eX-jdm4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/541/1%2AZX00M-DmsrigKap7wzGoQQ.png)

有了 Redux，事情会有所不同。假设你有一个组件，当一个按钮被按下时，你想做一些事情。你从哪里开始？我是这样做的。

1.  定义您的行动
2.  定义您的减速器
3.  将动作定义为组件中的道具
4.  在你的视野中连线

这里有一个简单的代码示例来解释这些概念。在这个例子中，我将展示如何编辑文本输入，当用户按下按钮时，它将调用一个动作来保存它。

首先让我们从**动作**文件开始。