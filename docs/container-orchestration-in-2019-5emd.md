# 2019 年的容器编排

> 原文：<https://dev.to/scoutapm/container-orchestration-in-2019-5emd>

*这篇文章最初出现在[童子军博客](https://scoutapm.com/blog/container-orchestration-in-2019)上。*

2019 年，您将如何部署您的应用？你已经开始使用集装箱了吗？根据最近的研究，你们中超过 80%的人是。如果您属于这一群体，您是否最初接受了容器的概念，但在现实中发现这种方法的复杂性使得很难进行权衡？社区意识到了这一点，并提出了一种缓解痛苦的方法，它被称为容器编排。因此，无论您是否在使用容器，让我们仔细看看*容器编排*，并找出您需要什么，它的用途以及谁应该使用它。

[![](img/d6f6472039578f654c28afede9512f6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1pe9m70h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gqub0l3lgi7bpesrtzgg.png)
*Portworx 2018 集装箱采用调查*

## 什么是容器编排？

如今，集装箱世界由码头工人主宰。事实上，大多数人将 Docker 和 container 作为同义词使用，尽管 container 比 Docker 本身存在的时间要长得多！我们在之前的一篇博文中介绍了 Docker 是什么的基础知识，并在另一篇博文中向你展示了如何让你的 Rails 应用与 Docker 一起运行。

因此，容器编排是以自动化方式管理容器的整个生命周期的过程。我们讨论的是部署、主机供应、扩展、运行状况监控、资源共享和负载平衡等。随着项目的增长，所有这些与管理容器相关的单个任务堆积起来，在您意识到之前，您已经遇到了一个相当复杂的问题。也许您的应用程序是由许多不同的互连组件组成的，每个组件都位于一个容器中。当您有数百个容器和多个节点时，您如何安全地部署并让所有的东西都很好地协同工作呢？这是容器编排解决方案试图解决的挑战。

orchestrator 位于这个复杂环境的中间，负责动态地将工作分配给可用的节点。它自动化并简化了许多与容器相关的基本任务，因此开发人员可以获得无缝体验。

[![](img/015dccf53792ebded934e83ddb23f5f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nv-i3Dph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zlll8v6n5x1vdetfu2g4.png)

容器编排的真正美妙之处在于，通过使用容器，你可以以一种灵活的方式关注你的应用程序，这种方式特定于你的项目需求，但你可以在任何你想要的环境中托管它们，比如谷歌云平台、亚马逊网络服务、微软 Azure 等。您不必因为一体化的 PaaS 解决方案不适合您公司的个人需求而感到受限制。你实际上是在创建自己的定制平台，然后将它推向托管解决方案。很有趣，对吧？因此，让我们看看当今最流行的容器编排工具，看看它们能为您做些什么。

## 库柏人诉码头工群诉 apache mesos

2019 年的三大容器编排解决方案是:来自谷歌的流行开源 Kubernetes 平台，Docker 自己的名为 Docker Swarm 的系统，以及 Apache 的 Mesos。

哪个最好，有什么区别？嗯，简短的回答是 Kubernetes 可能是大多数场景的首选，它的流行似乎不会很快停止。另一方面，Docker Swarm 是最容易上手的。而 Apache Mesos 提供了最大的灵活性，如果您有遗留系统和容器的复杂组合，这是一个很好的选择。

[![](img/13b67b58578bf582248af2037ce125f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hNEUgEes--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g9sakiv07mr6t2onsnyf.png)

### Kubernetes

[![](img/53674acfe72007c60540c1d9d3b1172e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sV5nxNNI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdmtr7zn1sclwlrfvld1.png)

到目前为止，Kubernetes 是这三种技术中最受欢迎的，因为它的特性集很棒，它得到了 Google 的支持，而且它是开源的。但是强大的能力带来了巨大的复杂性，这意味着新来者的学习曲线很陡。

Kubernetes 以无缝的方式构建在 Docker 生态系统之上，因此开发人员可以在源代码控制中维护他们的 Docker 文件，并将 kubernetes 后勤工作留给 DevOps 团队来处理。让我们来看看 kubernetes 使用的一些术语，我想这会让你对设计哲学有一个很好的了解。

一个**节点**是您的 kubernetes 系统可以使用的物理服务器或虚拟机。一个**集群**就是这些节点的集合，它们组成了你的整个系统；这些节点是 kubernetes 在其认为合适的时候自动管理的资源。

一个或多个 Docker 容器可以被分组为一个所谓的 **pod** ，它被集中管理，换句话说，它们被一起启动和停止，就好像它们是一个应用程序一样。

然后最后我们有了**服务**，这在概念上不同于 pod 和 nodes(可以上下)。相反，服务代表了应该一直运行的面向客户的东西，而不管 kubernetes 实际提供的是哪种资源。

Kubernetes 在容器领域越来越强大，显然是最受欢迎的容器编排工具。它与流行的云服务紧密集成，其庞大的用户群使其成为可靠的选择和投资。

### 码头工人蜂拥而来

[![](img/c257ed15903aa152dc0f795d3f3ec238.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HqCBeSRS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yltufvys4sbfhs8opa92.png)

如果 Kubernetes 的复杂性让您望而却步，或者您觉得不会利用它的所有特性，那么 Docker Swarm 可能更适合您的项目。这在概念上简单多了。Docker Swarm 是 Docker 团队的一个独立产品，构建在 Docker 之上，提供容器编排。

Docker Swarm 中使用了一些与 Kubernetes 术语意思相似的不同术语。例如，Kubernetes 集群的另一个术语是**群**。这是一个**节点**的集合，可以分解为 Docker Swarm 中的*管理者节点*和*工作者节点*。您可以将**服务**视为应用程序的单个组件，将**任务**视为 Docker 容器，为给定服务提供解决方案。

目前还不清楚 Docker Swarm 的未来会如何，因为 Docker 似乎已经完全接受了 Kubernetes，但如果你不需要 Kubernetes 的完整功能集，并且希望更快地启动和运行，这仍然是一个可行的选择。

### Apache Mesos

[![](img/8f2b5f4b211fb0cb14e7bca74b30787b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aDop14-W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v274lcu80r6fpieogkf4.png)

我想谈的最后一项技术是 Apache Mesos，它与我介绍的其他两种解决方案有点不同。它已经存在了很长时间，并不是像 Kubernetes 和 Docker Swarm 那样严格地为了管理 Docker 容器而创建的。相反，Mesos 可以更一般地描述为一个**集群管理工具**，它管理一个物理(或虚拟)节点集群，并向它们分配**工作负载**。但是它分配给节点的这些底层工作负载可以是任何东西。它们不仅仅局限于码头集装箱。容器只是 Mesos 可以管理的工作负载的一个例子。事实上，要将 Docker 容器与 Apache Mesos 一起使用，您实际上需要使用一个名为**马拉松**的附加组件，您不会从盒子里得到这个特性。

Mesos 确实很复杂，但是它有一些很大的优势。如果您没有完全受限于容器化的环境，并且可能有遗留系统和集成服务需要与容器一起管理，那么它会更适合。