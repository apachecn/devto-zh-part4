# 我们想要更小、更快、更安全的原生应用

> 原文：<https://dev.to/tauri/we-want-smaller-faster-more-secure-native-apps-5531>

## 形势室

2019 年，从编译到 javascript 用户界面框架的原生应用的制造变得比以往任何时候都更容易和更容易。尽管如此，初学者和经验丰富的开发人员在快速变化的安全和隐私环境中都面临着艰难的选择。在用户设备的半信任环境中尤其如此，在这种环境中，供应商用他们专有的“解决方案”将已经多样化的生态系统分割开来。

然而，应用程序领域的分裂并没有随着设备而结束，因为不仅有无数的前端框架，如 React、Angular、Vue 和 Svelte...但也有一系列包装像电容器，电子，质子原生和其他每天冒出来。再加上不安全的依赖管理和不完整的漏洞修复，我们剩下的是一个支离破碎的生态系统，赢家只是那些尚未受到攻击的幸运儿。

事实是，这些框架中的大多数从未被设想为存在于浏览器沙箱之外，而是突然被推入了一个非常敌对的环境，在那里开发团队求助于应用变通办法，如发布共享证书、入侵设备上的注册表或屈从于设备本身上本地主机服务器的不幸的常见集成。

[![Alt Text](img/bb31d7830a6c88ec2b2136c86aadb6d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6umbxzEO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cn1yw4fmt9nfsdtnzvm8.png)

## 一种范式的转变

**Tauri** 正面解决这些问题，因为它是从头开始设计的，旨在拥抱安全开发和创造性灵活性的新模式，利用 Rust 的语言功能，使您能够使用任何您喜欢的前端框架来开发您的应用程序。所有这些都在一个更加安全的分发环境中进行。

将 **Tauri** 作为您工具链中的一个组件，您将能够在创纪录的时间内为主要的桌面和移动平台设计、构建、审计和部署微小、快速、健壮和安全的原生应用。您可以在您喜欢的开发环境中，使用任何框架，甚至不需要了解 Rust 编程语言就可以完成所有这些工作。然而，如果你了解 Rust，你将有能力与底层操作系统和硬件进行更惊人的集成。

Tauri 团队已经使用 WebViews 完成了一些初步的概念验证(取得了非常令人鼓舞的结果，如 MacOS、Windows 和 Linux 上的< 3MB 二进制文件)。目前，我们正在完成 API，准备冒烟测试，研究交叉编译工具，甚至构建二进制评估工具。然而，我们对 WebView 方法并不完全满意，我们正在研究 Servo 和 Webkit 等替代方法。

## 取得联系

Tauri 应用程序还没有准备好迎接黄金时代，要达到这一点，在代码库和治理结构方面还有很多工作要做。然而，在我们寻求透明度和社区参与的过程中，我们借此机会邀请 Rust、Appsec 和 Dev 社区的所有人前来了解项目的进展情况，以及如何参与工作组。

Tauri 是一个致力于遵循 SFOSC [原则](https://sfosc.org/docs/principles/)最佳实践的组织。我们很荣幸也有责任邀请您访问我们的公共 [GitHub 项目页面](https://github.com/tauri-apps)，在我们的 [Discord 聊天服务器](https://discord.gg/SpmNs4S)上闲逛，在我们的[开放集体](https://opencollective.com/tauri)页面上捐款，或者只是关注我们的 [Tweets](https://twitter.com/tauriapps) 。

##### 关于作者

丹尼尔·汤普森-伊弗托特是 Tauri 背后的首席架构师和安全工程师。在过去的 13 年里，他一直是一名开源倡导者，也是可持续自由开源社区( [SFOSC](https://sfosc.org) )的活跃成员。