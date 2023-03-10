# 苹果应用分发的悲哀状态

> 原文：<https://dev.to/mikeyp/the-sad-state-of-mac-app-distribution-4oj5>

最新版本的 macOS 试图通过对应用程序进行更多的控制来提高平台的完整性和安全性。从 Mac OS X 10.8 开始，没有有效签名的应用程序不再在没有用户干预的情况下打开。现在，从 macOS 10.14.5 开始，这同样适用于未经苹果公证的应用程序。

随着平台安全性的加强，开发人员用来管理更新的工具已经变得过时和不安全。随着公证要求的实施，这变得更加难以控制。

## 是这样的

让我们来看看在 Mac 应用商店之外发布软件更新的两种最流行的方式: [Sparkle](https://github.com/sparkle-project/Sparkle) 和 [Squirrel](https://github.com/Squirrel/Squirrel.Mac) 。这两个项目都是开源的，并且是在一些新的安全实践之前编写的。他们依靠贡献者，其中许多人在空闲时间做出贡献。他们为社区提供了一种奇妙的、无价的服务。

现在让我们看看丑陋的事实:

成千上万人使用的这些第三方 Mac 应用程序更新程序依赖于五到八年前就已过时的安全 API。<sup id="fnref1">[1](#fn1)</sup>5[2](#fn2)

经过 3 年的开发，Sparkle 2.0 现在处于测试阶段，旨在支持应用程序沙盒。然而，经过仔细检查，它仍然依赖于 5 年前废弃的安全 API。 <sup id="fnref2">[2](#fn2)</sup> 。Squirrel 也依赖那些同样被否决的 API，但似乎没有任何计划开发来支持应用程序沙盒。

我们还能期待这些 API 工作多久？它们不是应该已经被移除了吗？

### 改变是艰难的

Sparkle 创建于 2006 年，有数以千计的应用程序依赖于它。同样，Squirrel 成立于 2014 年，是[电子](https://electronjs.org)应用的官方更新者。

为了适应新的安全 API，必须安装特权助手来处理实际的更新提取和验证。这些助手*必须*事先知道宿主应用的代码签名，否则 macOS 不会授权助手运行。这种变化看起来很微妙，但实际上，如果不彻底修改目标应用程序和更新程序之间的接口，就很难适应这种变化。这些更新程序怎么可能知道嵌入它们的应用程序的代码签名呢？这些应用程序可能还不存在！

可以说，没有一个更新程序可以在不完全中断开发人员工作流程或依赖它们的应用程序的情况下轻松适应新的安全 API。这也许可以解释为什么 Sparkle 和 Squirrel 都不能采用更新的 API。

## 每个人的手都被绑住了

很容易认为苹果的公证要求和 API 的反对是强迫开发者使用 Mac 应用商店的障碍。很难想象这不是部分真实的。然而，如果这是唯一的原因，他们会简单地删除那些在*年前*被标记为废弃的 APIs 然而他们没有。这样做会破坏 Mac 的第三方生态系统。

让应用程序采用应用程序沙箱和 API，最大限度地减少潜在的安全漏洞，这符合用户的最大利益。通过允许我们的应用程序使用不安全或过时的 API，甚至更糟的是，禁止使用它们的安全进步，我们对使用我们软件的人怀有敌意。

## 谁在乎？

修复软件更新需要时间(金钱),这些时间应该更好地用在开发产品上。这种困境是有经验的第三方 macOS 开发者继承和解决的，但是我们还剩下多少人呢？拿我来说，我的大部分收入来自编写后端 web 服务。我只是碰巧非常喜欢这个平台。

macOS 开发也有转变:使用[电子](https://electronjs.org)或[催化剂](https://developer.apple.com/ipad-apps-for-mac/)编写跨平台应用。让为 macOS 编写应用程序变得更容易很棒，但并没有解决保持生态系统健康的更微妙的问题。为了让它繁荣，应用程序不需要只在 macOS 上运行，它们需要*在* macOS 上。比什么都肤浅，需要个体关注。

不过，如果做得好，新人不需要担心这些细节，他们可以继续磨练自己的手艺。

## 一种可能的解决方案

一个新的(或打了补丁的)更新程序将由几个不同的组件组成:一个供 Mac 应用程序使用更新的框架，一个供开发人员分发更新的工具，以及一个提供提要的服务器。

最终目标将是一个现成的框架，应用更新和开发人员工具来发布和/或管理它们。

### 一个新的捆绑框架

捆绑框架可能是最大的变化。

应用程序更新程序，尤其是在应用程序沙箱中，依赖外部进程来执行实际更新。本文前面提到的不赞成使用的 API 需要被它们的后继者所取代，它们只允许在预先知道代码签名的情况下进行进程间通信。虽然这极大地减少了可利用的表面积，但对于共享框架来说，这是一个很难满足的要求:每个使用框架的应用都有自己的签名，直到运行时才知道。

然而，如果框架有一个带有预构建版本的孤立设计，这种复杂性几乎可以从范围中移除。该框架及其组件可以使用相同的证书进行签名，此时从任何捆绑它的应用程序使用都是安全的。

不管是否需要分发预构建的二进制文件，框架都应该是开源的。它需要能够完全自由地审查、分叉和修改。因为任何分叉或变化都需要用不同的签名来重建，所以这不会对使用正式版本的其他应用程序产生影响。 <sup id="fnref4">[4](#fn4)</sup>

### 其他组件

其他组件，开发人员工具和更新提要，都是“很好拥有”的特性，这使得实际“发布它”变得很容易🐿

开发者工具可以通过苹果的公证人进行飞行前检查，并在更新提要之前验证更新的签名。然后可以动态或静态地更新提要——假设越少越好。不过，我很清楚，人们不应该管理自己的服务器来发布更新。GitHub 发布版可能也是一种为开源应用提供 feed 的有趣方式。 <sup id="fnref5">[5](#fn5)</sup>

### 一笔勾销

尽管完全有可能更新现有项目以正确支持新的安全进步，但为了简化新项目和现有项目，最好从头开始。

虽然 Squirrel 依赖于与 macOS 相同的代码签名机制，但它的主要作者不再是项目的一部分。这本身没什么大不了的，但是它已经超过 18 个月没有有意义的发展了。它也是使用 *ReactiveCocoa* 编写的，这为那些寻求贡献的人设置了很高的门槛。

另一方面，Sparkle 的一些特性给支持 macOS 新安全模式的应用程序增加了太多的摩擦。它要求它的提要包括每个项目的签名散列，独立于代码签名，用于验证。如果申请*必须*公证和代码签署，我认为这是没有必要了。如果代码签名和公证检查成功，我们就可以很有把握地知道，我们不会用损坏或受损的代码替换应用程序。

Sparkle 还支持二进制增量，这非常棒，但会使新的更新程序的工作变得复杂。二进制增量使得部署提要更加困难，因为它需要跟踪以前的更新才能产生可用的结果。虽然这在某些时候可能很酷，但从一开始就需要支持它会增加很多工作。

最后，Sparkle 依赖于本文中没有提到的许多其他被弃用的 API。考虑到所有这些，听起来可能需要完全重写。嘿，也许我们可以叫它*闪耀 3* ！

无论如何，这些变化都足够显著，即使现有项目针对 2019 年进行了更新，应用程序集成它们的方式也将完全不同。如果你已经需要集成一个新的版本(可能是遗留代码)，那和重新开始有什么区别呢？除了额外的负担，什么都没有，这可能会减慢开发过程。😬

## 关闭思绪

我很想自己解决这个问题，但是我不知道我是否有时间或者需要解决这个问题。我已经在 Mac App Store 上发布了 [Emporter](https://emporter.app) ，并且确实想独立发布一个版本，但是现在对我来说转移焦点可能在经济上不可行。

我很乐意听到来自社区的反馈，并讨论我们如何向前发展。不要犹豫，通过[@ youngchadenet](https://twitter.com/YoungDynastyNet)联系。我的 DMs 是开着的。

* * *

1.  [`AuthorizationExecuteWithPrivileges`](https://developer.apple.com/documentation/security/1540038-authorizationexecutewithprivileg) 是当前使用的稳定闪耀版本。它在 OS X 狮子(10.7)中已被弃用。 [↩](#fnref1)

2.  [`SMJobSubmit`](https://developer.apple.com/documentation/servicemanagement/1431084-smjobsubmit) 是松鼠和闪闪 2.0 用的。它在 macOS Yosemite (10.10)中已被弃用。 [↩](#fnref2)

3.  [`SMJobBless`](https://developer.apple.com/documentation/servicemanagement/1431078-smjobbless) 是被弃用的 API 的继承者。它将特权“作业”(即更新安装程序)安装为 Mach 服务。它们只有在每个包的`Info.plist`中指定的代码签名需求匹配后才可达到，这在公共框架中很难维护。 [↩](#fnref3)

4.  代码签名要求确保应用程序只能运行它发布的未经修改的代码。 [↩](#fnref4)

5.  我已经为 [`emporter`](https://github.com/youngdynasty/emporter-cli) 工具编写了一个更新程序，由 GitHub 版本提供支持，使部署变得非常容易。 [↩](#fnref5)