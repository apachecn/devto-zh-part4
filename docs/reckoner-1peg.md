# 计算者

> 原文：<https://dev.to/nickhuanca/reckoner-1peg>

最近，我一直在努力支持[计算器](https://github.com/FairwindsOps/reckoner/releases)的一些用户体验和功能，这是一个开源工具，它与[舵](https://helm.sh)协同工作，使舵图在 kubernetes 集群上的可重复、声明性。它还支持指向 git 存储库作为图表源，这是 helm 目前还不支持的。

一个常见的用例是，当您想要在集群上安装`nginx-ingress`时，您希望能够将所有图表值签入源代码控制。下面是一个`course.yml`的例子，它是计算者用来安装图表的定义文件。

```
# course.yml
charts:
  nginx-ingress:
    namespace: ingress-controllers
    version: 1.15.1
    values:
      controller.ingressClass: "my-ingress-class-name" 
```

用一个`reckoner plot course.yml`运行计算者将确保 helm 用你提供的设置在你的集群中安装`nginx-ingress`。

在 [Fairwinds](https://www.fairwinds.com/) 我们使用 reckoner 为我们所有的集群安装核心基础设施，并管理与核心基础设施相关的图表值。

由于 reckoner 是在 python 中启动的，我们认为仅仅为了更容易的二进制分发而将其移植到另一种语言会有太多工作要做。幸运的是， [PyInstaller](https://www.pyinstaller.org/) 允许我们“编译”一个可以在 Linux 或 OS X 系统上运行的自包含二进制文件。这让我们的安装工作变得简单多了，也有助于我们的端到端测试，我们现在在 [CircleCI](//circleci.com) 和 [kind](https://github.com/kubernetes-sigs/kind) 进行测试。

如果你试用了 reckoner，但还有所欠缺，请随时通过 dev.to 联系我们，或者在 github 问题上展开讨论！我们已经做了一些改变来帮助人们在 CI/CD 中运行计算者，并且一直对它如何让人们的生活变得更简单感兴趣。谢谢！