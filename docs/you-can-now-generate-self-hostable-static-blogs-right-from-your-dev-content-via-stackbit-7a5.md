# 现在，您可以通过 Stackbit 从您的开发内容中生成自托管静态博客

> 原文：<https://dev.to/devteam/you-can-now-generate-self-hostable-static-blogs-right-from-your-dev-content-via-stackbit-7a5>

我们发布了一个新的特性，允许你从你的开发文章中自动生成和维护一个静态托管的博客。这有效地使 DEV 成为你自己托管站点的无头 CMS。

你已经可以从你的 RSS feed 中[交叉发布到 DEV，但是这改变了模型，呈现了一些迷人的可能性。](https://dev.to/settings/publishing-from-rss)

这是与专门研究静态站点生成的 [Stackbit](https://www.stackbit.com/) 的合作。它们代表了第一个与我们的平台挂钩的 Oauth 应用程序，我们期待在未来的合作中推广这一特性。

Stackbit 与存储静态文件的 GitHub pages 和提供服务的 Netlify 等服务集成在一起。它还允许你在静态站点生成器中进行选择，比如 Gatsby、Jekyll 和 Hugo。整个过程很神奇。

这里是我生成的网站:[https://royal-lime-c9165.netlify.com](https://royal-lime-c9165.netlify.com)🎉

这里是所有静态内容所在的回购，通过几次点击自动生成，但随后可根据我的意愿进行编辑...

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [本哈尔彭](https://github.com/benhalpern) / [皇家莱姆](https://github.com/benhalpern/royal-lime)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 堆栈位新鲜主题

本站由[www.stackbit.com](https://www.stackbit.com)生成，版本 0.2.73

# 在本地运行您的站点

1.  [安装 Hugo](https://gohugo.io/getting-started/quick-start/#step-1-install-hugo)

2.  从 [Stackbit 仪表板](https://app.stackbit.com/dashboard)的项目菜单中获取“stackbit-api-key”

3.  运行以下命令，将该键分配给`STACKBIT_API_KEY`环境变量:

    ```
     export STACKBIT_API_KEY={stackbit_netlify_api_key} 
    ```

4.  如果需要，运行以下命令从 Stackbit 获取其他网站内容:

    ```
     npx @stackbit/stackbit-pull --stackbit-pull-api-url=https://api.stackbit.com/pull/5d8c91658876b300134349f5 
    ```

5.  构建站点并启动 Hugo 服务器，启用草稿

    ```
     hugo server -D 
    ```

6.  浏览到 [http://localhost:1313/](http://localhost:1313/)

</article>

[View on GitHub](https://github.com/benhalpern/royal-lime)

### **T3】👉查看入门指南**

# **我为什么要这么做？**

这个问题问得好。

对于我们 DEV 的创始人来说，我们认为提供工具来帮助人们更好地管理他们的数据和在线状态是至关重要的。我们想成为一个分享、教学、讨论以及通过写作提升彼此的目的地，但我们不想垄断这个游戏。

有很多理由成为开发生态系统的一部分，但如果你想离开，我们不想让它变得很难。维护您的开发内容的镜像，您可以在这里和其他地方进行联合，这是防止 web 内容完全被集中式单一平台消费的一个组成部分。

我在这里写了更多关于这些想法的内容:

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## 贝纳塔尔项目:抵御数据黑洞

### 开发团队的 Ben Halpern 月 17 日 196 分钟阅读

#meta #opensource #projectbenatar](/devteam/project-benatar-fending-off-data-black-holes-499g)

关于我们的精神气质和前景的额外信息，这里有几个月前的另一个相关帖子...

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## Medium 从来就不是开发者生态系统的一部分

### 开发团队的 Ben Halpern 月 3 日 195 分钟阅读

#meta](/devteam/medium-was-never-meant-to-be-a-part-of-the-developer-ecosystem-25a0)

DEV 不仅仅是写博客，所以这些生成的网站必然是缺乏的。我们聚集在这里是有原因的，但我们坚信，一个健康的生态系统是个人网站仍然有一席之地的，迁移平台尽可能简单。

您不需要*将*交叉发布到个人网站。事实上，有很多很好的理由将流量集中到这样一个目的地，在那里你可以建立追随者和积累声誉。但是，总有一天你会希望你的内容由你的个人网站驱动。

我们正处于这项功能的第一天。可能需要一些额外的配置来实现它，所以请将这视为一个令人兴奋的概念验证，而不是完全充实的功能。如果您遇到破坏功能的边缘情况，请通过问题通知我们...

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ forem ](https://github.com/forem) / [ forem](https://github.com/forem/forem)

### 为社区赋权🌱

<article class="markdown-body entry-content container-lg" itemprop="text">

# Forem <g-emoji class="g-emoji" alias="seedling" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f331.png">🌱</g-emoji>

**For Empowering Community**

[![Build Status](img/a59640885320d80449880ba412cd1540.png)](https://app.travis-ci.com/github/forem/forem)[![GitHub commit activity](img/c636d7221527a9f58a1bcf7eb4f234ec.png)](https://camo.githubusercontent.com/64b9f7c7c5f41ec22113b61235256435cd61779a0554b0595b88b6011f94c60b/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f636f6d6d69742d61637469766974792f772f666f72656d2f666f72656d)[![GitHub issues ready for dev](img/a2f086092c44aa16ddbf13a9cf99fea1.png)](https://github.com/forem/forem/issues?q=is%3Aissue+is%3Aopen+label%3A%22ready+for+dev%22)[![GitPod badge](img/e87aed75ceeae18e025435d5084e1209.png)T11】](https://gitpod.io/#https://github.com/forem/forem)

欢迎来到 [Forem](https://forem.com) 代码库，这个平台为[开发到](https://dev.to)提供动力。我们很高兴你能来。在你们的帮助下，我们可以增强 Forem 的可用性、可伸缩性和稳定性，从而更好地为我们的社区服务。

## 什么是 Forem？

Forem 是用于构建社区的开源软件。为你的同行、客户、粉丝、家人、朋友以及任何其他需要聚集在一起成为集体一员的时间和空间的社区[参见我们的公告帖子](https://dev.to/devteam/for-empowering-community-2k6h)以获得 Forem 是什么的高级概述。

[dev.to](https://dev.to) (或者只是 dev)由 Forem 托管。这是一个软件开发人员的社区，他们撰写文章，参与讨论，并建立自己的专业档案。我们重视支持性和建设性的对话，为所有成员追求伟大的准则和职业发展。该生态系统涵盖从初学者到高级开发人员，欢迎所有人找到自己的位置…

</article>

[View on GitHub](https://github.com/forem/forem)

如果它在堆栈位一侧，我们将为您传递信息。

# **技术细节**

DEV 使用 markdown，并且通常与其他 markdown 驱动的框架兼容。正如你从上面的 GitHub repo 中看到的，我们确实有定制的丰富内容，它不像 markdown 一样统一呈现为 HTML。Stackbit 将这些丰富的内容转换成`iframe`，而我们通过普通的 HTML 内嵌显示这些内容。

Stackbit 充当方便而强大的管道，但具有最小的局限性，因为它与其他服务集成并生成标准的开源项目。这给了你更多的自由去打破生态系统。

这是一个渲染控制集中化的效率很难用这种东西复制的领域，但与这种可选性对每个人的整体积极好处相比，这是一个小细节。

这一点的实现可能会随着时间的推移而改变，这取决于我们所有人共同提出的想法。

# **往前走**

在将来...

*   随着时间的推移，这个特性和类似特性的功能和配置将会变得越来越有用。
*   更多的应用将被允许建立在我们的 Oauth 功能之上。请随意通过我们的 repo 为这一特性的推广做出贡献。
*   我们将推出越来越多致力于透明度、数据可移植性和去中心化的功能。我们有很多大事要做。

如果你想参与我们的任何开源计划，Hacktoberfest 将是最好的时机...

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/devteam) [## Hacktoberfest 六天后开始🎃🎉

### 开发团队的 Ben Hal pern 2019 年 9 月 25 日 2 分钟阅读

#meta #opensource #hacktoberfest #contributorswanted](/devteam/hacktoberfest-starts-in-six-days-3hi2)

注册参加 Hacktoberfest。我们将为 10 月 1 日的庆祝活动准备大量新的指导性贡献材料。

*如果你已经浏览了上面的内容，[下面是](https://dev.to/connecting-with-stackbit)开始使用 Stackbit 的说明。*

编码快乐！