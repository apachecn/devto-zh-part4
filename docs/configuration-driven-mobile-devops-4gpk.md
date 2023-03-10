# 配置驱动的移动开发操作系统

> 原文：<https://dev.to/mistryhardik/configuration-driven-mobile-devops-4gpk>

### 挑战

发布 5 星级应用是我们大多数人渴望做的事情，然而，如果你正在阅读这篇文章，你可能已经意识到，只有正确的技术才能实现。

> 你可以尝试使用这个 GitHub 库来关注这篇文章:[https://github.com/mistryhardik/ms-workshops](https://github.com/mistryhardik/ms-workshops)

编写和构建一个应用程序是我们关注的一个领域，但我们并不真的很费心去弄清楚如何分发它。市场上有太多的选择，变得相当抽象和模糊。

将这些选项中的每一个都视为在宝马或奔驰之间做出选择，它们都是对质量和豪华做出同等承诺的高性能车辆，它们都可以很好地将您从 A 点带到 b 点。然而，两者之间存在微妙的差异，这使它们在各自的细分市场/权利中成为不同的有力竞争者。

我们将探索这样一个工具来帮助您完成移动开发运维之旅，这个工具就是 AppCenter(以前称为 Visual Studio Mobile Center)。

注册 AppCenter 轻而易举(你可以从这里的免费账户开始: [https://appcenter.ms](https://appcenter.ms) )，然后配置计费，以获得额外的构建时间或在实际设备上进行测试。点击这里查看更多:【https://docs.microsoft.com/en-us/appcenter/ T2】

登录后，您需要定义一个应用程序，如果您正在跨客户工作或有一个大型团队，您也可以定义一个组织来对您将要处理的应用程序进行分组。
[![](img/2753efa00c0a9d3479e60902fcebaae7.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--sR4lB9N---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23td9a1wfcwhcr0c4eez.png)

好了，我们已经登录，现在，我们已经定义了一个配置为 Xamarin 应用程序的应用程序，请注意，您也可以选择任何其他风格的技术。
[![](img/b21a3345dc8ad798f4c41997c3fadecd.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Mis6tNOV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gnytzrmnucieuhff37pz.png)

接下来，我们点击 build 菜单(在左边，play 按钮类似于 guy ),这将要求我们配置包含我们想要构建的解决方案/项目的库。如你所见，在写这篇文章的时候，AppCenter 支持:Azure DevOps，GitHub 和 BitBucket 作为你的存储库提供者。在我的例子中，我将使用 GitHub 连接。
[![](img/bf4d1449fb33fd0bfb35eca6c1edf824.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NYxT23Kt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vuzdmaugq964wulsbo1v.png)

好了，一旦设置好了，我们就可以在我们选择的存储库下看到可用的分支。在我的例子中，让我单击并选择 development，然后我将单击 Configure Build 按钮(那个穿蓝色
衬衫的家伙:)
[![](img/63a39b6ea1053ccd6fd5bf8feacb98d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--acpEaLMB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8clah78bc0bhfu3yc6fa.png)

[![](img/b5eb893c9a722c68823bb167660641d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--deiX1HiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e539qaltv2coas5lrqqw.png)

接下来，根据应用程序的目标，你需要从各种设置中选择，这里是我的，因为我们正在尝试建立 Xamarin.iOS 项目。这将根据您试图构建的内容而有所不同。
[![](img/88db450ae01c931bf0e7acd122c230d6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--rdXZodvF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8clw5lt3fikcmwgkykg9.png)

> 你需要选择**。csproj** 构建 **Xamarin.iOS** 项目。
> 
> 如果您正在构建**原生 iOS** 应用，您将需要使用 XCODE 在您的工作区设置中定义**共享方案**。

一旦我们设置了所需的配置，单击 Save(如果您不打算立即运行构建)或 Save and Build，这将保存您的配置并立即触发构建。我点击了`Save & Build`

好的，几分钟后我们看到构建成功了，如果没有，有趣的是您应该能够找到构建失败的原因，并可以在您的存储库中进行更改来修复它们。

这是容易的部分，现在真正的挑战来了。您注意到了，我们能够将环境变量指定为键值对。我们如何在项目中用文件中的值替换它，或者当我们触发主文件的构建时，我们可以做什么来更新版本号，例如，开发版本是`1.0`，我们希望该版本增加`0.1`，因此通过主文件发布的版本应该是`1.1`，或者您可能希望替换 Info.plist 文件中的值。我们怎么能做到这些呢？

### 解

答案是`build scripts`。

`build scripts`是 bash 脚本，您可以在 AppCenter 的构建过程中执行`post clone`、`pre build`和`post build`。您需要为 AppCenter 适当地命名文件，以识别您的代码也包含构建脚本。[点击此处了解更多关于 appcenter 构建脚本的信息](https://docs.microsoft.com/en-us/appcenter/build/custom/scripts/)

`appcenter-post-clone.sh`和`appcenter-pre-build.sh`是我们已经在项目目录中的文件。

> 对于 iOS 项目，保留。sh 脚本文件与**在同一个目录级别。xcworkspace** 文件。
> 
> 对于 Android 项目，保留。 **/app** 目录下的 sh 脚本文件

### appcenter-post.clone.sh

`appcenter-post-clone.sh`脚本将做一些日常工作，比如下载一些实用程序到构建代理并安装它或者设置配置等等。我们将需要解析和编辑`.json`文件，为此我们将安装一个名为 [jq](https://stedolan.github.io/jq/download/) 的实用程序，并使用一个名为 [bc](http://www.gnu.org/software/bc/) 的实用程序来执行加法或其他算术运算。