# 坞站简介-基础知识

> 原文：<https://dev.to/azure/introduccion-a-docker-conceptos-basicos-51id>

[![docker.png](img/97c212d36a060bfdc98f55b9339e47bd.png)](https://postimg.cc/VJ2WzhgB)

*原由 Chris Noring - **[在此](https://dev.to/softchris/5-part-docker-series-beginner-to-master-3m1b)*** 撰写的文章

今天，我们将开始另一系列关于坞站的文章，从基本概念到更高级的概念进行解释。这一系列文章是以我的好友[【克里斯·诺林】](https://twitter.com/chris_noring) 的着作为基础的，他也是一个云倡导者，也是一个对像我这样的开源技术极为热情的人！

因此，我们团结一致，将特别为大家制作这一系列文章。但是，除了他写的文章外，∞视频关于每一个教学主题和内容稍多一些，以进一步支持我们的系列！

走吧。

## quees Docker？

∞这是一个很有趣的问题，我会尽量为大家总结一下！

**[【docker】](https://www.docker.com/)**是一个开源项目，旨在自动化应用程序的部署，作为可以在云中甚至在本地运行的容器。

docker 最有趣的部分是它在混合云的所有层上部署容器。其中， **[【蓝色】](https://docs.microsoft.com/azure/docker/?WT.mc_id=blog-devto-gllemos)** 。

[![image-2.png](img/160ef7ed56a534f5e9803995796b113a.png)](https://postimg.cc/YhpXPTDv)

还有一点很有趣:您可以在 Linux、Windows 和 MacOs 上运行映像容器。

我想你可以很好地理解坞站是什么意思，∞？但是如果你想了解更多细节，只需访问这里的链接

在这一系列活动中，我将提供:链接、资源、免费书籍，如关于这一主题的推荐材料和读物！因为没有什么比通过书籍和文献来学习新技术更好的了！

## 继续连载所需的资源:【学习对接-零对英雄】

不存在继续进行序列的强制性要求。∞因为这个系列的目的是教大家使用码头！

但是，我们将需要使用一些下载所需的资源，以便我们能够继续进行该系列。其中包括:

*   **[下载坞站社区版 CE](https://www.docker.com/community-edition) :** 下载坞站社区，了解如何从最基本到最高级执行命令！此版本是免费的，可以轻松下载(适用于 Windows、Linux 和 MacOs 用户)

*   **[【非蓝光账户】](https://azure.microsoft.com/es-es/free/?wt.mc_id=blog-devto-gllemos) :** 在这一系列中，我们将在云中测试一些图像和容器。在这种情况下，我们必须使用云端服务。在这种情况下，我们将使用蓝色。

*   **[学生用蓝色-免费](https://azure.microsoft.com/pt-br/free/students/?wt.mc_id=blog-devto-gllemos) :** 如果你是大学学生，有你大学的电子邮件账号，对你来说是好消息！你可以在: **[【学生用蓝色】](https://azure.microsoft.com/es-es/free/students/?wt.mc_id=blog-devto-gllemos)** 上获得完全免费的账号，需要信用卡，12 个月内马上赚取 100 美元的馀额使用蓝色服务！

*   **[Node.js](https://nodejs.org/en/) :** 随着我们在系列中向前推进，我们将使用一些示例，说明如何在 Node.js 应用中实现图像和容器的使用。

*   **[【Visual Studio 代码】](https://code.visualstudio.com/?WT.mc_id=blog-devto-gllemos) :** 由于代码中将有示例，所以我们使用 visual studio 代码开发我们的应用程序。

*   **[扩展 Docker - Vs 代码](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker&WT.mc_id=blog-devto-gllemos) :** 使用 Visual Studio 代码。

*   **[【蓝色工具- Vs 代码扩展】](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack&WT.mc_id=blog-devto-gllemos) :** 由于我们将使用蓝色，所以没有什么比使用 Visual Studio 代码的集成扩展更好的了，使其简单、快速、动态地使用 Visual Studio 代码创建蓝色图像或集成容器！

一旦您配置了整个开发环境和所有准备就绪，就可以继续进行系列！

## Por qué usar Docker？

Docker 将帮助我们创建一个可执行环境。此外，您还可以指定要使用的操作系统、不同库的确切版本、不同的环境变量及其值等。但最重要的是，您可以在此环境中独立运行应用程序。

一些让我们思考我们为什么使用坞站的要点:

*   **on board:**每当电脑有新的开发者时，您都必须设定、安装程式、SDK、开发工具、资料库、新增权限等。这可能需要几天甚至几周的时间。但是，当开发团队已经有了开发人员开始开发所需的所有资源的 dock 映像时，Docker 运行将足以在几分钟内安装所有内容！而且这大大加快了新开发者的‘T2’on board’T3’的过程。

*   **他们有标准的开发环境:**是的！这点很有趣！∞与坞站相比，可以创建一个**【dev staging】**，以及标准化的生产环境等等！这真是太棒了！从坞站/容器中，您可能会有类似的环境，但会稍有不同，当您尝试发现应用程序中的特定错误时，您可能需要花费大量时间来查找错误的主要问题。而且错误通常在源代码中，但由于环境的某些差异，可能需要很长时间才能确定问题的原因。在这种情况下，Docker 为开发团队提供了极大的便利，事实上，这种情况每天都很常见。

*   **啊-我...。我的机器工作:t1]使用坞站容器，这句话永远不会用到。为什么呢？！因为 Docker 创建了这些隔离的容器，您将准确地指定它们应该拥有的内容，所以您可以将容器发送给客户端，并且它们的工作方式与在开发/生产环境中一样。**

## Instalación de Docker

现在，我们已了解坞站和容器的基本概念，现在我们将坞站安装在我们的计算机上。我们要做的第一件事是运行**[【dock community edition ce】](https://www.docker.com/community-edition)**如果您是 Windows 用户，不知道如何在计算机上正确安装 dock，有视频教程通过以下链接教您如何安装:

📺[在 Windows 10](https://www.youtube.com/watch?v=Bxepw-C5a-s) 上安装坞站

在计算机上成功安装坞站后，您应根据以下 gif 查看信息:

[![gif-24a53d4bbf688045d.gif](img/fe515c3a462cecc4baff5b1c5a82a146.png)](https://gifyu.com/image/Elt0)

如果一切都像以前的 gif 一样工作，则准备好使用 docker 来开发应用程序！

## 结论

如前所述，我们将帮助大家了解 Docker，从基本概念到更高级的概念，从而提供免费链接、内容和课程，以补充大家的学习。

这些资源包括:

*   **[【在装有坞站的容器中编译 web 应用程序】](https://docs.microsoft.com/es-es/learn/modules/intro-to-containers/?WT.mc_id=blog-devto-gllemos)**
*   **[实施和运行一个装有 Azure App Service](https://docs.microsoft.com/es-es/learn/modules/deploy-run-container-app-service/?WT.mc_id=blog-devto-gllemos)** 的集装箱化 web 应用程序
*   **[数据科学虚拟机](https://docs.microsoft.com/es-es/learn/modules/interactive-deep-learning/?WT.mc_id=blog-devto-gllemos)**
*   **[用蓝色容器登记册](https://docs.microsoft.com/es-es/learn/modules/build-and-store-container-images/?WT.mc_id=blog-devto-gllemos)** 汇编和存储集装箱图像
*   **[Ejecutar contenedores de Docker con Azure 容器实例](https://docs.microsoft.com/es-es/learn/modules/run-docker-with-azure-container-instances/?WT.mc_id=blog-devto-gllemos)**
*   **[入门- Docker 文档](https://docs.docker.com/get-started/)**

另一个重要的问题是，每次写完一篇论文后，我都会在我的[【YouTube 频道】](https://www.youtube.com/user/l32759) 上做一段视频讲解和视频教学。因为有些人喜欢通过视频学习新技术。

如果你想知道视频日历，我将通过 GitHub 的存储库[【此处](https://github.com/glaucia86/docker-zero-to-hero-series) 提供

如果您对条款有任何疑问，可以在此处的评论中留下您的问题，如果您想提出建议，也可以了解！∞这个系列是为社区里的每个人制作的！

在下一篇文章中，我将介绍如何使用 Docker 使用 Express 开发 Node.js 应用程序。一篇更实用的文章！偶尔吧！

并且为了了解这个和其他的事态发展，∞在 twitter 上！

[![Twitter](img/9a13c0534f6448ca23e17b3afbfb6a4c.png)](https://twitter.com/glaucia_lemos86)

回头见！