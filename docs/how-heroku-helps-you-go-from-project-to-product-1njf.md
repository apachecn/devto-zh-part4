# Heroku 如何帮助你从项目走向产品

> 原文：<https://dev.to/heroku/how-heroku-helps-you-go-from-project-to-product-1njf>

你可能已经知道 Heroku 作为一个易于安装和快速开发的平台的声誉，它允许开发人员专注于开发，没有管理服务器和基础设施的耗时分心，但是让我们深入一点。在本文中，我将展示 Heroku 不仅仅是用于业余爱好的应用程序，并详细说明一些您可能不熟悉的特性，比如可伸缩性和巨大的灵活性。

我们将看看一些选择使用 Heroku 开发应用程序的公司，从他们的经验中抽取例子。

### 谁使用 Heroku

Heroku 可以为每一个开发者提供一些东西，从小型爱好应用程序到大型国际公司。您可以部署、运行和管理用 Ruby、Node.js、Java、Python、Clojure、Scala、Go 和 PHP 编写的应用程序，或者超越那些使用[自定义构建包](https://elements.heroku.com/buildpacks)的应用程序。

无论你是 Bonobos，有史以来最大的网络服装品牌，在成立后从 AWS 迁移到 Heroku，还是 Dubsmash 首次在 Heroku 上推出第二代产品，或者像 Moneytree 一样为你的客户安全地共享数据，几乎任何人都可以成功地使用 Heroku。(不要相信我的话，你可以在 Heroku 网站上看到这些公司用他们自己的话谈论使用 Heroku。)

### Heroku 与你同秤

增长是许多开发人员的目标，因此可伸缩性非常重要。随着范围和用户的增加，扩展使您的配置保持稳定和可用。有一些特定的扩展需求吗？你会很高兴地知道 Heroku 配备了垂直、水平或自动缩放功能。

> 我在此补充一点。Heroku 或任何工具集能以经济高效的方式扩展任何应用吗？不会。有些应用程序的架构很糟糕，在任何平台上都无法很好地扩展。Heroku 的 [12 因素](https://12factor.net)方法可以帮助你避免这个陷阱。

关于缩放的简短分析:

*   垂直扩展使 dynos(容器)更大(更多 CPU、更多 RAM)，因此更有能力更快地处理请求和更重的工作负载。
*   横向缩放是 Heroku 的超能力！这种策略添加了 dynos 的副本，这样每个副本可以处理总请求的一小部分。此功能通常被描述为“移动滑块”来缩放。
*   自动缩放会自动增加 web dynos 的数量。

Trailhead 是一个与 Heroku 一起构建的电子学习平台，最初是一个简单的工具，用于在 dev 布道者讲座和课堂上评估 Salesforce 的知识。现在，它是一个非常受欢迎的平台，自推出以来，规模每年至少翻一倍。早期，Trailhead 经历了“Astrogeddon”，这是一个周末发生的事件，开发者必须迅速扩大规模以适应令人震惊的流量高峰，后来又缩小规模。查看这个[Code【ish】播客](https://www.heroku.com/podcasts/codeish/18-the-making-of-trailhead)来了解更多关于 Trailhead 和 Astrogeddon 的信息。

Ipify 是 Randall Deggs 在 Heroku 上创建的一个可伸缩的 IP 地址查找服务，对于每个爱好开发者来说都是一个可伸缩性的光辉典范。最初这个项目从概念到启动只花了一天的时间，现在这项服务每月收到 300 亿次请求，甚至更多！Deggs 说 Heroku 是世界上最有效的开发者服务之一。

这些只是 Heroku 提供的扩展类型的几个例子，此外还有许多附加组件和其他功能(如管道)，旨在帮助您将应用从一个发展阶段提升到另一个发展阶段。

### Heroku 提供顺从

Heroku 符合 PCI、HIPAA、ISO 和 SOC 标准。这证明了 Heroku 是如何随着时间的推移而改变的；定期执行审计和维护，以建立和验证数据安全性。通过 Heroku Private Spaces，用户可以控制谁可以从哪里访问服务(通过 VPN 和 VPC 对等等功能)，这对 MoneyTree 等国际金融科技公司来说是一个非常宝贵的功能。使用 Heroku Shield Private Spaces，您可以更进一步，确保您的应用程序和数据处于符合 HIPAA 或 PCI 的环境中。无论您的应用程序是什么，Heroku 都能保证重要内容的安全。

### 迁移容易

我们已经确定你可以使用 Heroku“一路向前”,而且有无数的理由这么做！然而，我们不可能都是兰德尔·德格斯。有时候事情就是不顺心。如果出于某种原因，您决定迁移，这比以往任何时候都更容易。在谷歌搜索中输入“Migration from Heroku ”,你会得到一整页的结果——提供收费服务的公司、如何迁移到 AWS 的指南等等。假设你要从*迁移到*的基础设施是稳固的，[从 Heroku 迁移会很简单](https://dev.to/heroku/the-surprising-portability-of-heroku-apps-2n2h)。我发现许多开发人员在不中断服务的情况下完成迁移的例子。无论 Heroku 是您团队的垫脚石还是终点，使用该平台都有明确的价值，并且知道 Heroku 不会“锁定您”让我更有信心去探索它..

### 现在你知道了

当 Trailhead 需要在创建后 6 周内推出一款应用时，他们选择了 Heroku。当兰德尔·德格斯几乎是在一夜之间开始创建一项服务时，他选择了 Heroku。当这位 web 开发人员跳到 Heroku 时，她惊叹于该平台的简单和优雅。要更深入地了解 Heroku，请查看以下资源:

[理解 Heroku 神话与魔力关于复数视线](https://www.pluralsight.com/courses/play-by-play-understanding-heroku-myths-magic)
[什么是 Heroku？](https://www.heroku.com/what)
[打造你的 Heroku app](https://devcenter.heroku.com/start)
[关于 Heroku 的 6 个神话，被揭穿](https://dev.to/heroku/6-myths-about-heroku-debunked-a4b)