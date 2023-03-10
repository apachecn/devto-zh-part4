# Dockerless，第 1 部分:用哪些工具代替 Docker，为什么

> 原文：<https://dev.to/mkdev/dockerless-part-1-which-tools-to-replace-docker-with-and-why-4nd2>

> 免责声明:我不会试图重写关于集装箱和集装箱化标准等主题的现有内容。有很多精彩的文章和视频，我会把我认为最有用的链接起来。如果你不理解文章的某些部分，那么就按照我在文中放的所有链接去做。如果你还是有不明白的地方，那就在文章下面留言吧。我要么在回复中详细说明，要么延长文章。

### 为什么无码头？

这个系列的标题——“无码头”——需要一个解释。为什么你会*而不是*使用 Docker？为什么*会*使用 Docker？真的没有其他选择吗？早在 2013 年，我们——IT 社区——从第一次尝试就想出了完美的容器解决方案吗？

不完全是。行业尽最大努力避免我们在虚拟化技术方面遇到的情况，尽管有像 T2 lib virt T3 这样出色的工具，我们仍然没有一套标准供每个虚拟化提供商遵循。我们应该赞扬 Docker 公司实际上发起或积极支持了许多关于容器技术标准化的倡议。

自 2015 年以来，我们制定了[开放容器倡议](https://www.opencontainers.org/) (OCI)以及如何运行容器和管理容器映像的规范。此外，我们还有容器运行时接口( [CRI](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md) )和容器网络接口( [CNI](https://github.com/containernetworking/cni) )。如果您很难理解所有新的容器标准是如何组合在一起的，那么请观看 Scott McCarty 的[理解容器标准](https://www.youtube.com/watch?time_continue=20&v=rlj0UZlvGp0)演讲，他甚至演示了在不损失任何功能的情况下将一个容器引擎与另一个容器引擎进行交换。

如果您可以在 Kubernetes 集群中用 CRI-O(或任何其他引擎)替换 Docker Daemon，并且作为一名开发人员，从来不会注意到有什么不同，那么哪个引擎实际上在那里运行又有什么关系呢？就像容器让您不太关心主机服务器上运行的是什么一样，容器标准让您不太关心管理容器本身的是什么。毕竟，您关心 AWS EC2 是运行在 Xen 还是 KVM 上吗？你只需要一个虚拟机。或者一个容器。

您的 Kubernetes 集群背后的工程师需要维护、扩展和升级该集群，请务必关注。例如，他们可能会担心使用 Docker 的某些安全问题。作为另一个例子，他们可能认为在集群的每个节点上运行一个额外的守护进程没有任何意义。Docker 可能让容器大受欢迎，但这并不意味着它是使用容器的最佳技术。如果您对各种容器引擎的差异感兴趣，可以从这个比较开始: [Kubernetes 容器运行时](https://kubedex.com/kubernetes-container-runtimes/)。

作为一名开发人员，如果你开始依赖标准和一些公司不去搞砸这些标准的开发和支持，那么你可以自由地选择你的工具进行本地开发。如果你所有的工具最终都使用同一个 API，那么你就不能锁定一个特定的工具。所以“为什么*不是* Docker？”就是“因为我们不用再用 Docker 了”。那我们用什么来代替它呢？

### Buildah 和 Podman

我们将使用 [Buildah](https://buildah.io/) 来管理容器图像，使用 [Podman](https://podman.io/) 来管理容器。这两个工具都是由 RedHat 开发的，并且有适用于大多数基于 RedHat 的 Linux 发行版的软件包。更重要的是，从 RedHat Enterprise Linux 8 开始，[将不再提供现成的 Docker 包](https://www.redhat.com/en/blog/red-hat-enterprise-linux-8-beta-new-set-container-tools)。不过，您仍然可以从 Docker 库免费安装 Docker。

请注意，当我说“由 RedHat 开发”时，我并不是指这些工具仅由 RedHat 开发。Buildah 和 Podman 是开源项目，源代码托管在[容器](https://github.com/containers/)组织的 Github 上。RedHat 付钱给它的员工来开发这些工具，而且还有一个更大的开发人员社区来维护和扩展这两个项目。

### 使用 Buildah 和 Podman 的巨大缺点

在我们继续讨论使用 Buildan 和 Podman 的原因之前，让我首先清楚地说明与 Docker 相比，同时使用 Buildah 和 Podman 的明显和巨大的缺点:

1.  它目前只能在 Linux 上运行。没有像 Docker 那样方便的 Windows 和 MacOS 的包装器。在 Windows 的情况下， [WSL 2](https://devblogs.microsoft.com/commandline/announcing-wsl-2/) 的问题会小一些。
2.  没有 Docker Compose 可以替代 Podman。有一种方法可以只在本地运行符合 Kubernetes 的 Pod YAMLs，但是它还不成熟。不过，这种情况也可能会改变。我们将在本系列的最后一篇文章中讨论 Pod YAMLs。
3.  它仍然是非常新的，前进速度很快！

如果您是 Windows 或 MacOS 用户，您肯定会怀念 Docker 客户端工具的舒适性，因为您可能必须在手动启动的 Linux VM 中运行 Buildah 和 Podman。

如果您有一个非常复杂的 Docker Compose 设置(或者您甚至在 Docker Compose 的帮助下部署到产品中)，您将不会得到新工具集的替代。

但是如果这两点都不适用，那么从 Docker 切换过来之后，你几乎不会注意到有什么不同。但是你会得到什么回报吗？

### 从 Docker 转投 Buildah 和 Podman 的原因

#### 看到有替代 Docker 的

当您开始使用一套新的工具来管理容器时，您会很快意识到 Docker 只是另一个工具。你会看到没有“Docker 容器”，只有“容器”，也没有“Docker 图像”，只有“容器图像”。能够思考基础技术和原则，避免将思维局限于某一项技术，这一点很重要。

就像一个优秀的程序员意识到自己喜欢的语言只是众多编程语言中的一种一样，我们也应该意识到 Docker 只是与容器交互的一种方式。了解并尝试不同的选择是很重要的，它会给你不同的视角，让你的知识多样化。

#### 认识到容器到底是什么

一旦你停止思考“Docker ”,而开始思考“容器”,你不可避免地会了解容器到底是什么，并对“docker run”或“podman exec”等命令背后发生的事情有更深入的了解。如果你是一名网站开发人员，那么这些知识可能对你的日常工作没有帮助。如果你想更深入地了解事物如何运作的某些方面，这取决于你。我个人的观点是，理解所有东西是如何工作和配合的，这是每个工程师的重要技能。

#### 为未来做准备

当你试图在大脑中用“容器”代替“码头工人”的时候，这个行业也在这么做。如前所述，CRI-O 逐渐成为 Kubernetes 的默认运行时，不同的公司推出了不同的工具来处理容器。RedHat 推动 Buildah，而 Google 推动 Kaniko，总有一些不太知名的工具可能在某个时候成为主流。

你可以在 Docker 上下大注，只用那个。或者你可以了解行业内正在发生的事情，并为 Docker 不再是默认工具并被下一代容器技术工具所取代的时代做好准备。

### 关于低级工具的一个词

你可以超越波德曼一级，直接使用`runc`。从学习容器技术的角度来看，那将会更加有趣和有益。当您实际上想要将您的应用程序容器化并在本地运行时，这也会导致生产率的降低。Podman 的目标是成为一个用户友好的 CLI，一个 Docker CLI 的完全替代品，所以当你真正想完成工作时，使用 Podman 更有意义。如果你想看一篇关于只使用`runc`和其他低级工具来处理容器的文章，请在评论中 Ping 我！

### 前方道路

在下一篇文章中，我们将学习如何使用 Buildah 为 Ruby on Rails 应用程序构建容器映像。我们还将看到什么是真正的容器图像，并看看 OCI 图像规范。在最后一篇文章中，我们将使用 Podman 以类似 Docker Compose 的方式完全自动化开发环境。

这里我们不打算用一些人为的例子。带有[所有支持服务](https://mkdev.me/en/posts/services-for-online-education-startup)的 mkdev.me 核心 web 应用在 Buildah 和 Podman 的帮助下被迁移到本地开发环境中的容器中。mkdev.me 是这个练习的一个很好的例子:我们有一个中型的 Ruby on Rails 应用程序，它使用 PostgreSQL 数据库作为主存储，使用 Redis 作为缓存和后台作业。我们还将 Mattermost 作为我们消息平台的核心，这需要它自己的数据库。它不像只有一个 web 应用程序和一个数据库那样枯燥和简单，也不像有十几个应用程序的微服务设置那样复杂。

* * *

还在为集装箱的事情困惑吗？[我们的 DevOps 导师](https://mkdev.me/en/specializations/devops)将帮助您获得这些技术的深度知识和经验。

*这是[一篇由](https://mkdev.me/en/posts/dockerless-part-1-which-tools-to-replace-docker-with-and-why)[基里尔·希林金](https://mkdev.me/en/mentors/fodoj)撰写的mkdev 文章。*