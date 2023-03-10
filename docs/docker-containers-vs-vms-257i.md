# Docker 容器与虚拟机

> 原文：<https://dev.to/npentrel/docker-containers-vs-vms-257i>

**虚拟机(VM)**和 **Docker 容器**是两种有助于资源利用的技术，使您能够在同一基础架构上的隔离环境中运行多个应用。

给定一个 VM 或容器上的终端，任何一个都将表现得好像它实际上是一个专用机器。这使得很难区分虚拟机和容器，尤其是如果您刚刚开始使用 Docker。为了帮助你，这篇博文将向你展示两者是如何运作的，同时指出它们的相似之处和不同之处。这篇文章不需要预先了解虚拟机或容器。

#### 虚拟机和容器

虚拟机已经存在很长时间了，允许物理机运行多个操作系统和多个隔离的应用程序。虚拟机类似于“常规”物理计算机:它们运行操作系统，安装了许多库，并运行应用程序。区别在于虚拟机没有自己的专用硬件。相反，虚拟机通过模拟专用硬件的软件来使用物理机的底层基础设施。为虚拟机虚拟化硬件的软件称为虚拟机管理程序。

<figure>

[![Multiple VMs that run on top of a hypervisor which regulates access to underlying infrastructure.](img/8dcdb3f3b8f58b7a17ab63cefa45e370.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SuE1tIOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b7ln74cl0po6wrd75ja4.png)

<figcaption>Source: https://docs.docker.com/get-started/#containers-and-virtual-machines</figcaption>

</figure>

每个虚拟机都有自己的操作系统，这使得虚拟机需要相当多的存储和其他资源。为了减少每个运行的应用程序所需的资源，人们提出了容器。

虽然容器化已经在各种操作系统上存在了一段时间，但社区支持 2013 年首次发布的 Docker。在此期间，技术界(尤其是 DevOps 界)一直在寻找能够更高效地部署和扩展微服务的解决方案。Docker 设法通过使 Linux 的 LXC 容器化更容易使用和更容易访问来满足这种需求。他们承担了通常用于配置容器的繁重工作和规划，并将其转化为编写 Docker 文件，然后由 Docker 文件为您完成这项工作。正是这种易用性让 Docker 如此受欢迎；Docker 从一开始就是开源的事实当然也有所帮助。

容器允许开发人员通过打包应用程序及其确切的依赖项来最小化他们所需的资源。容器不是运行在管理程序上，而是运行在安装在操作系统上的容器运行时环境之上。虚拟机使用虚拟化硬件，而容器使用主机操作系统的底层资源。

<figure>

[![Multiple containers that run on top of Docker which is installed on the host operating system which regulates access to underlying infrastructure.](img/05b8bd67fa8687e00341552a7bae4972.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lCruyLtB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hz62tbfvkjntiqq4778h.png)

<figcaption>Source: https://docs.docker.com/get-started/#containers-and-virtual-machines</figcaption>

</figure>

将虚拟机和容器与房屋和公寓进行比较有助于使这些差异更加明显:一所房子有自己的水管、自己的互联网电缆和自己的垃圾处理系统。另一方面，公寓由他们的公寓楼管理这些资源，可以访问整个建筑的资源，而不必每个人都有自己单独的版本。家规确保公寓住户不会互相激怒，并公平分享所有资源。

在这个比较中，虚拟机就像房子一样。它们都有自己的操作系统和专用资源(如内核、文件系统、进程树、网络堆栈)。虚拟机访问 CPU、存储、RAM 等。通过虚拟化硬件的管理程序。

集装箱更像公寓。他们只有运行应用程序所需的东西。它们共享底层操作系统的资源(例如内核、文件系统、进程树、网络堆栈),这些资源由容器运行时环境隔离，容器运行时环境围绕特定容器可以访问的资源实施规则。总的来说，这种模型使容器成为一种更加轻量级的解决方案。

容器和虚拟机在运行方式上的这些差异使得容器的供应速度更快，也意味着它们经常(通常应该)被用作不可变的非持久结构。容器的生存和死亡在很大程度上不是你应该关心的事情(除非它们同时死亡！).另一方面，虚拟机通常具有更长的生命周期，您花费更多的时间来配置它们，并且您可能会关心它们是否发生了什么事情。

在较高的层面上，容器和虚拟机之间的差异可以概括为:虚拟机被认为是一种虚拟化技术，而容器被认为是一种应用交付技术。

#### 了解更多

这是我们对虚拟机和容器的简明概述。如果您有兴趣了解更多信息，请查看:

*   [Docker 行话:从 Dockerfile 到 Container](https://dev.to/npentrel/docker-jargon-from-dockerfile-to-container-942)
*   [码头教程](https://www.youtube.com/watch?v=wjvyN_r-zkk)
*   [码头支票簿](https://github.com/npentrel/SmoothDevOps/raw/master/cheatsheets/01%20Docker%20Cheatsheet.pdf)
*   [码头工人入门指南](https://docs.docker.com/get-started/)
*   [奈杰尔·波尔顿的码头深水潜](https://www.amazon.com/Docker-Deep-Dive-Nigel-Poulton-ebook/dp/B01LXWQUFF)
*   [orlandgan . net/dock/](http://orhandogan.net/docker/)的缩写