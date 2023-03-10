# 了解 MVVM 架构中的数据流

> 原文：<https://dev.to/productivebot/understanding-the-flow-of-data-in-mvvm-architecture-487>

我们经常需要在应用程序的不同视图之间传递数据或建立通信。MVVM 架构使得开发 Android 应用程序变得容易，然而一些概念可能一开始会有点混乱。有时，对于视图或视图模型的角色以及它们之间的相互关系，会有一些误解。本文将展示 MVVM 架构的 Android 应用程序的不同组件之间的交互，以帮助您清楚地了解数据流。

### ***观察者*设计模式是理解 MVVM 数据流的第一把钥匙**

观察者模式允许一个或多个数据的*观察者*被一个*可观察对象*或*主体*更新。在 MVVM 架构的应用程序中，这种模式的使用促进了不同组件(模型、视图-模型&视图)之间的数据流，并保持这些层的分离。

作为 Android 架构组件的一部分， *Observer* 模式构成了诸如 RxJava 和 Android 自己的 *LiveData* 类等库中 *Observable* 基类的基础。数据的观察者通常是视图，有时是视图模型，观察对象通常在模型或视图模型层定义。模型本身也可以作为数据的观察者来执行任何后端处理。

### **使用可观察数据流的异步数据流模型**

这是理解 MVVM 中数据流的第二个关键，与调用函数并期望通过调用 return 语句或回调函数得到结果的通常方式相反。

操作的结果可以通过用应用程序模型中定义的可观察对象注册一个观察器来观察。这有助于持续的数据更新，直到视图被破坏。

值得注意的是，同步请求在 MVVM 应用程序中仍然占有一席之地，例如，快速获取某些东西的状态，如检查应用程序设置。

然而，使用异步流编程通常是更好的实践。例如，将数据查找的逻辑从内存更改为网络或数据库(两者都需要后台执行)意味着只需要更改模型实现，而视图和视图模型代码保持不变。

## MVVM 组件间交互概述

为了理解数据应该如何在 MVVM 应用中流动，我们应该首先从高层次上了解不同组件之间的关系以及它们如何相互作用。

[![MVVM_Data_Flows_1.png](img/079f02f972318ef039b03dae36353a44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pUNEFtmV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r82oczwhgpxhtxnk1r7a.png)

** *图 1* * -显示了 MVVM 组件之间的概念性应用和可能的交互*

在 MVVM 架构中，视图与视图-模型交互进行数据绑定，视图模型与模型对话。视图从不与模型直接交互。本质上，视图模型促进了应用程序模型和视图之间的交互。

还可以推导出其他一些要点:

*   视图之间不应该直接通信，所有数据都应该通过视图模型传递(最好将视图状态定义为数据模型，并在模型中处理或保存它，即使它是暂时的)。
*   视图(例如活动、片段、Android 视图)可以绑定到一个或多个视图模型。
*   视图模型可以绑定到一个或多个模型。
*   模型之间可以直接交互。这可以通过共享组件来实现，如数据库、首选项或内存中的共享对象。

让我们更详细地看一下 MVVM 组件之间的交互。这些交互是分开呈现的，以便更清楚地了解事物是如何工作的。

## 视图和视图-模型交互

[![MVVM_Data_Flows_2.png](img/2222b257790bb6225baa474670a2d656.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--skBlrm1Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qt4e8hrkrfy6kjxztevd.png)

** *图 2**-Android 应用中视图和视图模型之间的交互序列*

**1。获取视图模型**的实例——这通常发生在视图创建期间，例如 *Activity.onCreate()* 或 *Fragment.onCreate()* 。在 Android 架构组件中，这是通过调用 *ViewModelProviders.of()* 函数来实现的。

**2。开始观察数据**——视图通过在视图模型中注册一个*观察器函数*或*回调*来开始观察数据更新，比如一个 *LiveData* 实例。这将持续视图的生命周期。Android *LiveData* 类会自动处理这个问题。

**3。获取或设置数据**——向视图模型发送请求，通过公开的函数或属性获取一些数据或进行一些更改(甚至创建一些东西)。

**4。更新视图**——当可观察对象(例如 LiveData 实例)的值被结果更新时，它调用注册的观察者以便更新视图。

## 视图-模型和模型交互

[![MVVM_Data_Flows_3.png](img/57d1c7812db87b97ad554f05ae77d691.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d2yN2bIb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f0rsper9esl25ocqffz0.png)

** *图 3**-Android 应用中视图模型和模型之间的交互序列*

**1。获取模型**的实例——当创建视图模型时，应该通过依赖注入或访问模型 singleton 或其他方式获取对模型的引用。当视图模型被破坏时，释放对模型属性的任何引用，例如从 observables 中注销，例如 *ViewModel.onCleared()* 。

**2。开始观察数据**——视图模型订阅来自模型的数据更新。这可以是订阅一个 RxJava *可观察对象*，也可以是对模型中定义的 LiveData 实例的直接引用

**3。获取或设置数据**——当视图调用视图模型函数时，视图模型可能会进行一些内部处理，但通常会代表视图将请求转发给模型。

**4。更新观测器**——一旦模型完成处理，它就更新相关的可观测值。如果 RxJava Observable 被更新，这通常会导致视图模型中的实时数据实例被更新。因为一个或多个视图可能已经观察到了实时数据实例，所以它们将得到通知并相应地被更新。

## 结束语

MVVM 架构巧妙地促进了 Android 应用程序中不同视图和模型之间的数据流动。Android 架构组件的引入使得实现一个像样的 MVVM 应用架构变得更加容易。

为了在 MVVM 架构的应用程序中成功实现数据流，需要理解的关键点是:

*   观察者设计模式*(以及核心类，如架构组件 LiveData 和/或 Rx Observable 和 LiveData 中的 Observable)*
*   数据流的异步特性*(通过分离对模型的请求并通过可观察对象观察结果来实现)*
*   视图和视图-模型交互
*   视图和视图模型的生命周期
*   视图-模型和模型交互

一旦理解了数据流，您就可以在定义视图、模型和视图-模型以及实现它们之间的交互时做出明智的决定，从而产生一个结构良好且干净的代码库。

## 订阅

这篇文章[最初发布](https://www.productivebot.com/posts/understanding-the-flow-of-data-in-mvvm/)在我的网站【ProductiveBot.com。如果你喜欢这篇文章，请务必在 ProductiveBot 网站上订阅，以便在我发布新内容时得到通知。