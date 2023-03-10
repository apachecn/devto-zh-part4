# 6 分钟 PWA 概述(第 1 部分)

> 原文：<https://dev.to/andreib123/pwa-overview-in-6-minutes-part-1-nn1>

这是我最喜欢的技术话题，**渐进式网络应用**。目标是给*写一系列与 PWAs 相关的文章*，所以请告诉我你**是否努力**去理解/实现任何功能。

也许你从 Google events 或者网上的一些随机文章中听说过这个*(成为)*的流行语。如果没有，没事，我们就从**开始**开始。

在过去的几个月里，我在 PWAs 上做了**两次演讲**，总共有超过 **50 人参加**。焦点集中在两个方向，第一个是*(与本文相关)*，介绍**什么是 PWA**以及**为什么**有用，第二个是从头开始实现一个现场 PWA*(未来文章)*。

你会发现:

*   什么是 pwa📳

*   你能从中获得的利益👍

*   带有**统计数据**的生产 PWAs 示例📈

*   PWA **特性**📝

但是首先，**重要**注意*(如果没用过 PWA)* ！从 Chrome 进入 www.twitter.com，点击菜单**“安装 Twitter”**，或者从你的 Android 设备点击**“添加到主屏幕”**弹出。如果你是苹果用户，点击**“分享”**，然后**“添加到主屏幕”**。

目标是真正地**在这篇文章的信息之前**看到 PWA 在行动。

我们走吧！

[![enter image description here](img/4089ca4fd85fc423b131c5a3807f405b.png "PWA Overview Banner")](https://res.cloudinary.com/practicaldev/image/fetch/s--M63Ory7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/dU8xyqdwbOH9V3FR2Jm8FqwJp4zXY68HENj7k3PcEJw82fwtNGNmbnjiKqkxwaeR4Dd68k2etjDt)

## 什么是 pwa

基于谷歌所说的:

> “渐进式网络应用程序是使用网络技术构建的网站，但是,
> **的行为和感觉就像一个应用程序,**”

这个定义的主要思想是，你可以从头开始开发一个 web 应用程序，甚至可以用一些功能升级现有的应用程序，使其像本地应用程序一样运行。*(手机或桌面)*

现在一个更随意的解释是:

> “渐进式网络应用只是服用了所有正确的 T2 维生素的网站。”

因此，PWA 可以被视为 web 应用程序的升级，以满足全球用户的新需求。超过 **50%** 的互联网流量来自移动设备，所以你的网络应用程序应该*做更多的响应*来适应。

## PWA 检查表

[![enter image description here](img/79a1d1e5974a7c7e744cdbb094c5892e.png "PWA Checklist")](https://res.cloudinary.com/practicaldev/image/fetch/s--G9f2HTNM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-6yZclFn_Ht_z7okPMLsES3aarCiEyKdetTaRNMqoWzHH-SoAUAGr9YVyxAAHjD0YFN2cDjCstRO)

**HTTPS -安全**

任何 PWA 都应该在 **HTTPS** 上空发球。如果这个要求没有得到尊重，你的申请将不会是进步的。

**连通性独立**

您的 web 应用程序应该支持弱网络和脱机支持。这两个功能都可以使用服务工作者和缓存来实现。

**类 App**

应用程序的结构应该具有本机的**方面**。为此，我们可以使用服务人员第一次加载**应用程序外壳**，并在下次访问时加载。

**可发现的**

做 web 应用可以通过**搜索引擎**找到。

重新订婚

吸引用户的移动应用的主要功能是**推送通知**。有了服务人员，您可以为您的 PWA 实现它们，并在任何地方使用它们。

**新鲜的**

应用程序可以使用服务人员*(后台同步)*用新功能**更新**。

**反应灵敏**

当然，应用程序应该能够**响应**并且在任何类型的屏幕上都表现良好。

**可安装**

您的应用程序可以通过浏览器**安装**,之后表现得像本地应用程序一样。

## 案例分析

**1。推特**

先说 Twitter 的情况:

> “超过 80%的用户使用移动设备，Twitter 希望他们的移动网络
> 体验更快、更可靠、更吸引人。”

Twitter 实现了他们应用程序的 PWA 版本，并设法使其成为最常用的客户端。这个应用在桌面上也取得了很大的成功。

以下是他们展示的一些统计数据:

*   将每个会话的页面增加了 65%

*   将**发送的推文增加了 75%**

*   跳出率下降了 **20%**

考虑到他们用户的数量，这对他们来说是一个巨大的成功。

[![Twitter UI](img/b264e1065c964dfee52b75811ac19bfa.png "Twitter UI")](https://res.cloudinary.com/practicaldev/image/fetch/s--jkbUE9hP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/DiL7ioN3ohYsR9FGQKOuMsBtsi4XaH3aLNdtkOVx_KuqAiN5hhXMgQXjKe3lr43tnyxM-kv_8KdM)

**2。Pinterest**

Pinterest 发现了他们网络应用的一个问题，他们宣称:

> Pinterest 注意到它的网站体验很慢，只有 1%的访问者注册或安装了移动应用。这让他们
> 开发了一款渐进式网络应用。

在实施 PWA 后，他们也有一些有效的**统计数据**要分享:

*   花在移动网络上的时间增加了 **40%**
*   用户生成广告收入增加 **44%**
*   与**的互动增加了 60%**

其中最重要的一个，在我看来，是收入的第一个。这表明这项技术可以在大型组织中成功应用，并对业务/收入方面产生积极的*影响*。
[![Pinterest UI](img/af66e21b26fe9b7a71c246b18e0c0c8f.png "Pinterest UI")](https://res.cloudinary.com/practicaldev/image/fetch/s--b2RubJ34--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/dBcTZnU68ggx461SDT9xyunDVEXveADTXzJgzBJWUcvBeTh38Ys85FUybrPrFmKLLlj0RKmt1aD9)

## 特征

**1。快速加载**

谷歌表示:

> " **53%** 的用户会放弃一个超过 3 秒钟的
> 加载时间的网站！"

记住这些信息，PWAs 的一个重要方面就是快速可靠。实现这一速度的一个特殊方法是使用服务工作者来缓存应用程序外壳。

[![Service Worker example1](img/4a7b546efc09065b1dc45acbc96eb250.png "Service Worker example1")](https://res.cloudinary.com/practicaldev/image/fetch/s--eCDuD77---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/QD_B390MSM0OTVQXX1hh6aeAOb2HTjWYJkUsocbg19X90tPgxR2-MA1bbsAzL2KFM53TcF3RS2sK)

下面是一些简单的网络请求。当用户打开应用程序时，外壳可以由服务人员直接**提供服务**，服务人员可以拦截浏览器发送的任何网络请求。

好处是用户在所有资源被加载之前得到了快速反馈。
T3![Service Worker example2](img/15dc15aca479566a7f507eae668c289a.png "Service Worker example2")T5】

另一个好策略是让服务人员在开始时执行与业务相关的**数据请求**，直到浏览器绘制出应用程序外壳，内容才可以加载。在这个例子中，时间减少到**几乎是**的一半，这代表了巨大的差异。

**2。可安装的**

> **“添加到主屏幕**，有时也称为 web 应用安装
> 提示，让用户可以轻松地在他们的移动或桌面设备
> 上安装渐进式 Web 应用。”

它的作用是，在你的屏幕上添加一个快捷方式，你可以在一个单独的 T2 进程中启动应用程序，而不是直接在浏览器上。它在移动和桌面上都可以工作，但主要目标是移动用户。

PWA 可以从 Safari 安装，但它需要**两次额外的触摸**，浏览菜单。解决这个问题的方法可以是自定义**实现一个类似于 Chrome 的横幅**，并在后面手动实现安装**。

[![Install PWA examples](img/1e564e32c0c2fdc2d71e1b720ae0d9ed.png "Install PWA examples")T10】](https://res.cloudinary.com/practicaldev/image/fetch/s--AkZrMC6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/fxkYF3JWgqmWsYDk71fh0_H4ZCS9bKcQCA6-hZn2Uky-zO2kNAIGGidIofOUy9MDzsomkPIyeSYP)**

 ****3。多个平台**

PWAs 可以支持多种平台*(浏览器、移动设备、桌面)*，并且将特别针对其中的每一个平台。因为没有什么是完美的，当我们提到 **Safari** 时，会有一些**限制**。

[![Browser comparation](img/5bf5d6e4899772b09b52097205d698c9.png "Browser comparation")](https://res.cloudinary.com/practicaldev/image/fetch/s--V56RywaS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/N7u_AIlV83xXhXKdWUyt2N3EZrDchK9lcLzOjRNsJCI5w7jrIp5FeU62x7X1YVaXYuYhnnbPmxUf)

是的，我知道你在想什么。看起来 PWA 只能在 Chrome 或者其他非苹果浏览器上实现，但事实是**在中间的某个地方**。

即使 Safari 不支持像**推送通知**或**安装横幅**这样的重要功能，PWAs 仍然可以通过其他好处和**的一些定制**为苹果粉丝带来价值。

**4。离线工作**

离线工作这是一个大胆的声明。这是 PWAs 的一个目标，但是你可以只实现*一些功能*。

例如，你可以**缓存应用外壳**，如果你有一个类似于 dev.to 的平台，还可以缓存**访问的文章** *(数据)*。在这种情况下，如果网络不稳定或者根本没有连接，用户仍然可以阅读加载的文章。

一个更高级的特性是当连接恢复时，使用**“后台同步”**提交离线用户操作。

**5。接合**

接合点与**推送通知**紧密相连。这是保持用户对应用程序动态感兴趣的主要特性。

[![Push Notification schema](img/24299573f8422286f537d6fe8d405c5b.png "Push Notification schema")](https://res.cloudinary.com/practicaldev/image/fetch/s--XfftaiMK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/jnK-xzVmfu-g4crRNCCWrfCzj12uOcl7_ldZcVbTtYKeyHzZjIjA1HZaz2lI3yTlwvuMytJnBanN)

在上面的模式中，给出了一个使用**Google Cloud Messaging***(GCM)*将通知推送至 PWA 的实现示例。

首先，您可以询问用户是否同意接收通知。如果答案是肯定的，向 GCM 请求一个**令牌**，然后将它存储到您的服务器中，与设备绑定。

当你想发送一个**通知**时，你可以简单地发送一个**请求**给 GCM，使用令牌和一些附加信息，比如消息或图标，GCM 会将通知发送到你的设备。感谢服务人员，你的客户可以**理解**和**显示**通知。

[![Conclusion banner](img/4557b28e152ae625397500e4c49dd1f0.png "Conclusion Banner")](https://res.cloudinary.com/practicaldev/image/fetch/s--lFP1ebUX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/fxZ73S1VZMoS9w71i_pSm6XoyaoEOYnTsepbPI1n8IRiaOJKrAECGeUMG0k-Zy1cEMSPqpoLyLk0)

这是一篇高层次的文章，介绍了 PWA 的主要方面。要完全理解 PWAs，你需要进一步研究。如果你想*写更多关于与艾滋病相关的特定话题*，请留下你的评论。

PWAs 代表了一种快速开发应用程序的方法，这种应用程序可以在所有平台上运行。如果你的用例可以忽略这些限制，尤其是那些与 iOS 相关的限制，它可以成为你的**移动网络困境的一个很好的解决方案。**

即使你不需要一个移动应用程序，在你的网络应用程序中增加安装 T2 和离线功能的能力也是一个巨大的 T4 升级。这些功能是最容易实现的，我将在以后的文章中介绍它们。

PWA 的两个主要问题是: **iOS 限制;**和* *用户还不熟悉** 作为 PWA 的网络应用。

这是一项正在发展中的技术，它有很大的潜力来解决来自 T2 用户行为的变化，我认为意识到这一点很好。可能有些情况下它是最合适的。

如果你有任何**问题**或**建议**，请在评论中告诉我。

编码快乐！**