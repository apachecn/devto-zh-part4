# 角度新闻:Javascript 疲劳、应用分析、渐进式网络应用和 GraphQL

> 原文：<https://dev.to/thisdotmedia/angular-news-javascript-fatigue-app-analytics-progressive-web-apps-and-graphql-pp6>

与 Doguhan Uluca、Sumit Arora、Maxim Salnikov 和 Uri Goldshtein 一起讨论 Angular 的许多令人兴奋的新发展！

观看下面的视频，了解处理 Angular 问题的最佳实践。熟悉 Redux Beacon，轻松进行角度应用程序分析，或者深入了解渐进式 Web 应用程序(PWA)的热门趋势。

**JavaScript 最佳实践**

*如何避免 JavaScript 疲劳——用 Doguhan Uluca 处理 JavaScript 问题的最佳实践*

Excella Consulting 的 JavaScript & Cloud 专家 Doguhan 已经为企业部门构建了许多移动和 web 解决方案。他对于当今的开发人员在审查大量现代 web 库和所需工具时所面临的问题有着卓越的洞察力。

Doguhan 与不同经验水平的开发人员一起工作，发现当前的 JavaScript 开发是不可持续的。目前，当面临开发问题时，许多人都急于找到一个 JavaScript 库来帮助解决问题。然而，这不仅增加了 JavaScript 的疲劳程度，还导致了外部依赖性。这在很大程度上可以归因于可供使用的软件包和库的数量。

最佳实践不应该是安装新的库，而是解决现有库或所用技术中的技术问题。开发人员必须通读文档，首先仔细检查解决方案是否可用，然后继续搜索可以帮助解决特定问题的外部库。

Doguhan 在这次采访中进一步阐述了评估工具和库的最佳方法。

[https://www.youtube.com/embed/hjDKnpq13c8](https://www.youtube.com/embed/hjDKnpq13c8)

**Redux 信标和分析**

*使用 Redux Beacon 进行简单的角度应用分析 Sumit Arora 访谈*

了解如何使用 Redux Beacon 通过 Sumit Arora 轻松进行应用分析。

Sumit 和他在 Rangle.io 的团队创建了一个新的开源角度库，名为“Redux Beacon”，为开发者提供用户转化率等分析。该工具通过特殊事件将数据从 Angular 应用程序发送到目标分析引擎，如 Google Analytics、Google Tag Manager 或自定义内部目标。发送的数据被存储并转换成机器学习格式。它还提取关于用户行为的有价值的见解，例如使用了多少特定功能，或者特定的用户群(即地理分组的人口统计)可能正在做什么。Sumit 希望借助这些见解，开发者能够更好地预测用户的未来行为。

[https://www.youtube.com/embed/7qkuXzXp7dA](https://www.youtube.com/embed/7qkuXzXp7dA)

**PWAs 和 Angular 移动工具包**

*使用 Angular Mobile Toolkit 的渐进式网络应用——与 Maxim Salnikov 讨论这一热门趋势*

Maxim 是 Angular 的谷歌开发专家，NgViking 会议的组织者，也是挪威奥斯陆 Angular meet-up group 的领导者。

最近，他的兴趣集中在使用 Angular 的渐进式 Web 应用程序(PWA)上。他解释了为什么构建渐进式 web 应用程序是 Web 开发中最热门的趋势之一，以及如何利用 Angular Mobile Toolkit 来制作“Web 渐进式”应用程序。

随着越来越多的用户从桌面应用转向移动应用，需要考虑一些事情。网络可靠性和速度是需要记住的最重要的特征之一。PWA 通过提供一个支持离线用户体验的框架来提供问题的解决方案。随着开发人员将更复杂的特性转移到客户端，PWA 提供了更大的性能改进，这是它的一系列好处之一。

对于一个 PWA“就绪”的 web 应用程序，大约有 10 条规则需要遵循。然而，只要该应用程序是建立在渐进的布局上，可以在许多屏幕尺寸上调整大小，并在 HTTPS 提供服务，它就可以转换为 PWA。此外，正如大多数 web 应用程序一样，它们也应该是可链接和可搜索的。

Angular Mobile Toolkit 的目标是自动创建 PWA。该工具包用于自动生成应用程序外壳，该外壳创建支持用户界面所需的最少 HTML、CSS 和 JavaScript。服务工作器是浏览器在后台运行的脚本，它通过呈现缓存的内容来提供离线功能。这确保了用户在重复访问时能够获得即时可靠的良好性能。

pwa 的典型例子包括 Twitter 移动网络应用、金融时报和福布斯。

使用任何工具，在使用缓存实现 PWA 时都应该小心。仔细考虑要缓存的文件数量和大小，以避免达到缓存限制。此外，开发人员应该确保在缓存新版本之前清理旧的缓存版本。

[https://www.youtube.com/embed/Dvmij7RORQw](https://www.youtube.com/embed/Dvmij7RORQw)

**GraphQL +角度分量**

*Uri Goldshtein 解释为什么应该在应用程序中使用 GraphQL】*

angular-meteor.com Apollo & GraphQL 团队的专家 Uri Goldshtein 解释了为什么像银行(即 Capital One)这样的大企业，或者像脸书和 AirBnb 这样面向消费者的公司，采用 graph QL 来帮助他们的数据管理。他还阐述了它对企业和客户双方的好处。

GraphQL 是一种强大的应用层查询语言，它在客户机和服务器之间创建数据契约。客户端开发人员可以在数据规则约束(由服务器端开发人员设置)内对数据创建 GraphQL 查询。客户端开发人员可以高效地查询他们需要的数据，服务器端开发人员可以高效地描述供他们的客户端使用的数据。

设置 GraphQL 服务器很容易，开发人员可以使用他们选择的语言来完成。这是可能的，因为它能够独立于客户端和服务器端开发人员工作。它可以被后端开发人员、产品经理或客户端开发人员采用，每个人都可以从使用它中享受性能优势。

当使用 GraphQL 和 Angular 组件时，它是所有组件集中共享的层，而不必担心其他服务器上的数据结构。这种框架可以提高效率，因为客户端的服务器请求更少，从而减少了网络延迟，并提供了更强大的用户体验。

[https://www.youtube.com/embed/34dQ07nJCJY](https://www.youtube.com/embed/34dQ07nJCJY)

我们希望你喜欢这篇文章。有关 Angular 的更多信息，请务必查看 [http://angular.io](http://angular.io/) 。

这篇文章是由莎拉·诺派恩和 T2·李美玲共同撰写的。

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。