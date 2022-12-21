# Git 驱动的基础设施作为原始 Kubernetes 发行版上的代码

> 原文：<https://dev.to/simbo1905/git-driven-infrastructure-as-code-on-origin-kubernetes-distribution-4hcj>

在 [uniqkey.eu](https://uniqkey.eu) 我们只需将更改推送到 GitHub，就可以自动部署在 AWS 上运行我们的应用程序的所有 Kubernetes 配置。我可以说这是一件好事，因为这是关于效率、开发运维以及基础设施即代码的。这是真的，但它错过了魔术。以这种方式驱动您的基础设施为我们提供了🐐🏭💨🦄。这是一个牦牛刨毛厂，为团队的鼓风炉提供动力，让团队变得更加强大。

我们甚至编写了一个 slack bot 来编辑 git 中的配置文件并创建 pull 请求。当一个新的开发人员加入我们的团队时，他们可以通过挂在 slack 上和机器人聊天来将他们的第一个代码推向生产。是的，他们是🗣️🤖🌈。当我们运行连续部署时，每个人都可以看到 slack 中发生了什么。这是一段七分钟的视频，展示了实际情况:

[https://www.youtube.com/embed/TGjI6AT4QC4](https://www.youtube.com/embed/TGjI6AT4QC4)

我们认为其他人可以使用我们的方法，所以我们把所有东西都放在 GitHub 上，命名为 [OCD](https://github.com/ocd-scm/ocd-meta) 。是的，我们🦄🎁🌈。这是一系列关于 OCD 如何结合一些伟大的开源技术来经营一个成功的创业企业的文章中的第一篇。在 AWS 上的 Kubernetes 中经营一家拥有多个 web 应用程序和移动后端的企业是一个大话题。所以我会把它分成小块，你可以在你的笔记本上运行。

但我为什么称之为强迫症呢？因为它在 OKD 上运行，这是一个关于过分自动化的糟糕双关语，抱歉。我想我应该解释一下背景。

Origin Kubernetes 发行版 OKD 是最受欢迎的 Kubernetes 发行版之一，它使自助开发成为现实。正是开源项目推动了 OpenShift，所以我将互换使用术语 OKD 和 OpenShift。我们在 OpenShift Online Pro 上运行我们的业务应用程序，这是一个 CaaS(容器编排即服务)。我们只需在 Kubernetes 集群上租用空间，其他人就会修补它和操作系统。我们只支付一小部分集群的费用，就能得到一个成熟稳定的解决方案。我们只需要管理运行我们的 webapps 和移动后端 API 的 Kubernetes 配置。openshift.com 服务团队保持 AWS 上的托管集群正常运行，并安装安全补丁。然而，OpenShift 是基于开源的 OKD，所以没有锁定，你可以在任何云上运行它。

如果您还没有发现为什么 OpenShift 是一个很好的起点，这里有一个视频，通过简单地在 web 控制台中输入 git URL 来构建和部署一个[react js 应用程序:](https://realworld.io)

[https://www.youtube.com/embed/7xK8la3AGtI](https://www.youtube.com/embed/7xK8la3AGtI)

该视频强调了 Origin Kubernetes 发行版如何致力于成为一个将您的代码转化为一个动态系统的解决方案。git URL 通过一个模板运行，用于构建和部署 node.js 应用程序。该模板创建所有必要的 Kubernetes 对象来提取您的代码，构建一个容器图像，并将其推送到内部容器注册中心的图像流中。它还设置了一个部署对象，该对象监视图像流中的推送事件，以部署任何更新。最后，有一个服务来平衡 pod 的负载，并有一个路由将它们暴露给外部世界。开发人员只需输入一个 git URL，就可以创建大量软件定义的应用基础架构！

权力越大，责任越大。我们希望我们所有的 Kubernetes 配置都在源代码控制之下。这允许我们像对待代码一样对待我们所有的 Kubernetes 应用程序基础设施，以便我们可以自动化部署。我们希望代码审查、持续集成和持续交付到 Kubernetes 上。我们想要完整的🐐🏭💨🦄。在这一系列的文章中，我将首先在您的笔记本电脑上运行 OCD 演示，快速浏览它的功能。在那之后，我将介绍一些伟大的工具，这些工具将强迫症有机地结合在一起，而不仅仅是这些部分的总和。首先，我们将从头开始在 Minishift 上运行第一个教程，介绍如何设置 Kubernetes 配置部署管道。