# 选中 Xamarin 中的新复选框。表单 4.1 和概述输入工具控件

> 原文：<https://dev.to/eatskolnikov/checking-the-new-checkbox-in-xamarin-forms-4-1-and-overviewing-inputkit-control-17pd>

*一语双关……*

随着 Xamarin 的释放。表单 4.1 新增了一个[复选框控件](https://docs.microsoft.com/en-us/xamarin/android/user-interface/controls/buttons/check-box)。它看起来是这样的:

[![](img/20b17b5009b592c740f7bcb5547ca0f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DP-4Vvml--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.torib.io/wp-content/uploads/2019/07/Screen-Shot-2019-07-05-at-4.12.25-PM.png) 

<figcaption>图片直接取自文档[https://docs . Microsoft . com/en-us/xa marin/xa marin-forms/user-interface/checkbox](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/user-interface/checkbox)</figcaption>

下面是在 XAML 和 C#中实例化它的两种方法

复选框有一个针对选中状态(IsChecked)的可绑定属性，您也可以处理 **CheckedChanged** 事件。

#### 一件小事

iOS 上的控件是圆形的，如果你想要它是方形的，你可以使用[可视化 API](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/user-interface/visual/) 。

目前，CheckBox 控件不包含文本属性，也不在其旁边呈现标签，要实现这一点，您需要显式包含一个标签控件，并将其与其对齐。

结果看起来像这样:

[![](img/3b0e4a5d3f97a45200add5b7204f0100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZXHt1Mfs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.torib.io/wp-content/uploads/2019/07/Screen-Shot-2019-07-05-at-6.11.48-PM.png) 

<figcaption>左边是 iOS 版右边是安卓版</figcaption>

### 其他选项

如果您正在处理一个遗留项目，或者出于某种原因，您无法更新到 Xamarin。Forms 4.1，但您仍然希望包含一个复选框，这里有几个选项供您选择:

1.  **使用** [**开关**](https://docs.microsoft.com/en-us/xamarin/android/user-interface/controls/switch) **控制。从实用/功能的角度来看，这与复选框非常相似，因为您可以管理开/关(真/假)状态。用户界面当然不是复选框，但是如果你需要的只是功能，这就很好了。**
2.  **使用 [Xamarin。表单. InputKit](https://github.com/enisn/Xamarin.Forms.InputKit) 库**。它已经发布了一段时间，所以团队有时间加入一些简洁的功能，比如能够为控件设置标签、单选按钮和设置自定义复选图标等等。

使用 Xamarin。Forms.InputKit 库

像 Xamarin 生态系统中的大多数第三方库一样，它非常容易设置，并允许您访问各种健康的控件。现在，我们将关注复选框功能。我可能会在后面的文章中介绍完整的库。

要设置库，只需执行以下操作:

*   包括 **Xamarin。获取所有项目中的包。**
*   通过调用**插件在目标项目中初始化它。input kit . platforms . IOs . config . init()；**或者**插件。input kit . platforms . droid . config . init(this，savedInstanceState)；**相应地。
*   在 XAML 中引用库的名称空间**xmlns:input = " clr-namespace:Plugin。InputKit . Shared.Controls 程序集=插件。InputKit"** 。
*   将输入命名空间添加到要包含的控件前面。

要创建一个复选框，您应该编写如下代码:

*   [![](img/a71dbb245cc77909a1567aa47887dc61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pGP9zcza--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.torib.io/wp-content/uploads/2019/07/Screen-Shot-2019-07-05-at-4.57.07-PM.png)

*   [![](img/028c2cd9f3459665b77a2b40aa40f39a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dkXhIgii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.torib.io/wp-content/uploads/2019/07/Screen-Shot-2019-07-05-at-4.57.17-PM.png)

和新的 Xamarin 一样。表单复选框控件，InputKit 包含一个用于被选中状态的可绑定属性，称为 **IsChecked** ，您可以通过事件处理程序 **CheckChanged** 或命令 **CheckChangedCommand** 来监听被选中状态的更改状态。

如前所述，这个库已经存在了一段时间，它比新的 Xamarin Forms native Checkbox 多了一些东西，比如可以包含复选框的标签文本和要呈现的复选框类型。这些是不同的类型:

[![](img/25519b22b789758b5bae84d2e6babff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--01QzHyi7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.torib.io/wp-content/uploads/2019/07/Screen-Shot-2019-07-05-at-4.02.53-PM.png) 

<figcaption>图像直接取自资源库</figcaption>

我在这里写了一个示例项目，如果你想看看的话[https://github.com/eatskolnikov/CheckBoxSample](https://github.com/eatskolnikov/CheckBoxSample)

这就是所有人，一如既往，我希望这些信息对你有用，如果你喜欢它，请随时与你的同行分享并查看其他帖子。谢谢，再见！

文章[检查 Xamarin 中的新复选框。表单 4.1 和概述输入控制](https://blog.torib.io/2019/07/05/checking-the-new-checkbox-in-xamarin-forms-4-1-and-overviewing-inputkit-control/)首先出现在 [Enmanuel Toribio](https://blog.torib.io) 上。