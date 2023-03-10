# 普罗班多{XAML 热重装为 Xamarin。表单}

> 原文：<https://dev.to/luciomsp/probando-xaml-hot-reload-for-xamarinforms-3h38>

我知道你来这里的原因，也知道了解更多关于其他数字媒体的内容很诱人，但首先，我要提醒你最后一次记住这种感觉，那就是，你在等待将应用程序重新部署到仿真器或设备上，只是为了测试或查看使用 xamarin . forms 时 UI 中的最新更改。

[![](img/e1065aeb187e97454f941a8ea0eb4fa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3xH5uUbo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ahd52EkGOBysx9_fG.jpeg)

以往[洗发精开发者峰会](https://xamarindevelopersummit.com/)推出了我们大家都期待已久的特色，我们在 xamprin . forms 上开发的，就是公布了**xampre hot reload for xamprin . forms，**这基本上使我们能够对 xampr 用户界面进行更改并查看它们

好吧，我想你们中的大多数人在读这篇文章时不能否认，这是或‘t0’是一个有点麻烦的事实，需要为每一个微小的视觉变化重建并重新开始调试，还要等待 10 秒钟到 1 分钟(取决于设备)，才发现变化不是你想要的我们知道，一路上有人试图克服这种情况，例如，有人预览了 XAML 和 xamarin live player，但它们都不同于 Hot Reload 允许我们做的事情。

**那么-什么，∞是热充电吗？**

XAML hot reload for xamarin . forms 是允许我们加快用户界面开发的过程，也就是说，每次通过 xaml 更改我们正在运行的应用程序时，我们只需按“保存”键即可！，无论是使用模拟器还是物理设备，我们的更改都已显示在屏幕上。

听起来很棒吧？。让我们看看它的工作原理:

[https://youtu.be/Y3nGkzbc0JE](https://youtu.be/Y3nGkzbc0JE)

正如我们所看到的，任何 XAML 文件类型(即页面、App、资源或 Shell)都支持更改，并且当使用 XAML 热重装编译应用程序时，可以与所有第三方库和控件一起使用。

**主要特点:**

*   在调试模式下部署应用程序后立即查看 XAML 更改。
*   适用于 Windows 和 macOS 的 Visual Studio 2019。
*   funciona tanto como para Android como para iOS。
*   支持仿真器和物理设备。
*   支持插件、第三方库、SkiaSharp 等。

### 摘要

XAML Hot Reload 来帮助大大减少在 UIs 中迭代所需的步骤和时间。使用 XAML 热重装无需做任何特殊的事情。就像平常一样编译和部署你的应用程序，从那时起你只需要对 XAML 做任何更改。也就是说，您不需要在应用程序中安装任何额外的 nuget 软件包，也不需要在应用程序中执行任何初始化逻辑。

这是因为 XAML 热重装**使用**调试器，而不是网络连接，所以无论是否连接都可以工作。编辑 XAML 并保存更改。重新加载时，导航状态将保持不变。如果使用 MVVM 模式，则链接到 ViewModel 的用户界面状态保持不变。

[![](img/b70d56b118aa6ebc2391861a7b0e783d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B2-cNtqV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AkrMBNzImlpkwi_Yx.gif)

使用的示例:[可绑定布局](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)

XAML Hot Reload 将于 2019 年晚些时候提供，但您可以注册参加预览阶段。

### 预览 Privada

如果你想尝试 xam 热充电，你现在可以注册获得一个‘t0’预览，可在以下链接中找到全部信息:[:https://devblogs . Microsoft . com/xamn/xaml-hot-reload](https://devblogs.microsoft.com/xamarin/xaml-hot-reload)。

值得注意的是，它们是按批添加到组中的，所以如果不能立即获得，则不必担心。我们要记住，任何反馈都是持续改进的重要因素。

我确信这一新特点将使我们所有人的生活都变得更好、更轻松。

### 更多信息

*   宣布 XAML 热重装为 Xamarin。表格
*   [Xamarin。表单 XAML 热重装](http://aka.ms/XAMLHotReload)

编码重装快乐！

* * *