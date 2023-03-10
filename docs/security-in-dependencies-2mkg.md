# 依赖关系中的安全性

> 原文：<https://dev.to/wagslane/security-in-dependencies-2mkg>

选择正确的依赖项是一项艰巨的任务。假设一个应用程序的开发者是世界上最好的程序员，那么“最好”的做法就是独自编写整个代码库。这将消除低级开发人员的错误、漏洞和恶意入侵。

我们在 [Qvault](https://qvault.io/) 认识到我们**不是**世界上最好的开发者，但是我们仍然采取适当的措施来减少我们对外部代码的依赖。

问题是，我们喜欢快速行动，以便能够在市场上竞争并提供新功能。当有问题的轮子被广泛使用，被同行评审，并且我们认为它是稳定和可信的时候，我们尽量避免重新发明轮子。

在这篇文章中，我们想要解释我们使用哪些依赖项，以及避免哪些依赖项背后的决策过程。

## 我们能写出来吗？

[![](img/7d37ebeac693702628a8a33ca1a593bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fbz2YlDV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/08/photo-1501504905252-473c47e087f8-1024x768.jpeg)

当考虑新的依赖关系时，这应该是第一个被问到的问题。如果它适合一个项目的范围，那么由我们自己编写和拥有代码几乎总是最安全的。相关问题包括:

*   我们要花多长时间来写它？
*   我们需要整套吗？我们能重写我们需要的部分吗？
*   这是我们的专业领域吗？
*   我们的执行会更好吗？(不要自己写密码术)
*   这种语言的标准库已经有这种功能了吗？

## 回顾

[![](img/55c0d74ff0bbb596b0d294c0bbf68d4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fL5cnCDy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/08/photo-1516714819001-8ee7a13b71d7-1024x683.jpeg)

如果我们决定不应该自己编写代码，我们会找到一个有效的候选包。我们查看代码并确保:

*   代码有测试吗？它们相关吗？
*   图书馆的架构好吗？代码质量高吗？
*   有多少贡献者？
*   有多少其他项目依赖于这个包？
*   项目是否得到积极维护？最后一次发行是什么时候？
*   此项目有依赖项吗？如果是这样的话，我们也需要回顾一下(最好**尽可能避免子依赖**)。

## 锁定版本

对于任何更安全的依赖项，锁定版本是很重要的。例如，我们可能已经完成了对软件包' **abc** '的可靠审查，并确定 **v1.4.4** 是稳定和安全的。我们想确保我们不会在没有检查变更的情况下不小心更新了那个包。像 yarn 这样的包管理器保留了每个依赖项的数字指纹，这样一旦 v1.4.4 代码被引入，就不能被恶意换出。

更新中的漏洞不受此功能保护。为此，我们尝试使用**' 1 . 4 . 4 '**vs**' ~ 1 . 4 . 4 '**或 **'^1.4.4'** 。通过不包含波浪号或插入符号，我们告诉 yarn 我们不想自动更新这个包。

当以这种方式锁定版本时，我们需要更加注意定期检查和手动应用更新，因为很多时候新的更新会修复旧的安全漏洞。

参考:[https://stackabuse.com/caret-vs-tilde-in-package-json/](https://stackabuse.com/caret-vs-tilde-in-package-json/)

## 组织和跟踪

我们将依赖项分成了两组:

*   开发依赖性
*   属国

例子:[https://github.com/Q-Vault/qvault/blob/master/package.json](https://github.com/Q-Vault/qvault/blob/master/package.json)

因为 [Qvault](https://qvault.io/) 是一个电子 app，技术上来说 *devDependencies* 和正常的 *dependencies* 没有区别。我们仍然以这种方式组织它们，以跟踪哪些依赖项用于*构建*我们的应用，哪些依赖项用于我们的应用。

例如，[电子构建器](https://github.com/electron-userland/electron-builder)和[电子公证器](https://github.com/electron/electron-notarize)用于打包和公证我们的 Mac OS 应用程序，但[简单键盘](https://github.com/hodgef/simple-keyboard)在我们的应用程序中运行，因此我们认为它是一个更高的潜在威胁，并对其保持额外的密切关注。

## 沟通

当我们决定要使用一个依赖项时，特别是如果它是一个不太为人所知的依赖项，我们通常会联系开发人员询问问题，并确保它得到良好的维护。例如，我已经多次联系负责维护[简易键盘](https://github.com/hodgef/simple-keyboard)的[Hodge](https://github.com/hodgef)，他是一个很棒的资源。

我们希望这个快速指南能让你对我们的依赖管理过程有所了解，并给你一些构建你自己的项目的好建议。注意安全，享受建筑的乐趣！

莱恩·瓦格纳

延伸阅读:[https://research.swtch.com/deps](https://research.swtch.com/deps)

github:[https://github.com/q-vault/qvault](https://github.com/q-vault/qvault)

首页: [https://qvault.io](https://qvault.io)

依赖关系中的[安全帖子最早出现在](https://qvault.io/2019/08/21/security-in-dependencies/) [Qvault](https://qvault.io) 上。