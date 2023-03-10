# Webpack 的状态

> 原文：<https://dev.to/thisdotmedia/the-state-of-webpack-2lpf>

[https://www.youtube.com/embed/OdBX54PXfUY](https://www.youtube.com/embed/OdBX54PXfUY)

Webpack 是一个模块捆绑器，允许你编写模块，然后编译到浏览器中运行。最近几个月，JavaScript 社区中的许多开发人员已经采用 Webpack 作为他们项目的实际模块捆绑器。一些使用 Webpack 的突出项目是 Angular 的 Angular-CLI 和 React 的 Create-React-App。

Webpack 核心团队的 Sean Larkin 在 This.JavaScript 上向我们介绍了 Webpack 的最新情况

# web pack 社区

Webpack 2 是几个月前发布的。最近版本的主要重点是文档。Webpack 2 的广泛社区参与需要对贡献者的更多支持。这些资源可以在 webpack.js.org 找到。

除了文档，Webpack 2 的发布带来了品牌的复兴。在 Webpack 的这个迭代之前，没有核心团队或媒介。现在，客户和支持者可以在 medium 上阅读一些提示和技巧或合作伙伴文章。

# web pack 中最近的活动

Webpack 最近加入了 Open Collective。这让个人有机会成为产品的利益相关者和股东。自加入以来，通过赞助商和支持者的支持，估计每年的预算为 8.3 万美元。有了这些资金，webpack 团队已经能够促成一个更好的开源平台。

Webpack 对开源社区的未来以及这种合作所带来的工具抱有很大的希望。与许多其他平台不同，Webpack 不是由特定的公司或框架赞助的，这意味着社区创建的工具完全以用户需求为中心。

# 增长与规模

自启动以来，Webpack 取得了显著增长，从 2016 年 4 月每月 50 万次下载开始，到目前每月超过 500 万次下载。

由于社区和贡献的扩大，一个更新和更丰富的 Webpack 生态系统现在已经存在。曾经被认为是黑盒的工具，现在是一个包含更好的分类和接受来自社区的贡献的产品。

加入该组织的新存储库包括 Webpack-contrib、Webpack-cli 和 Webpack-canary。

所有的加载器和插件都从最初的 webpack 组织转移到一个名为 Webpack-contrib 的独立组织工具中。这个回购协议促进了一套通用的标准，适用于每一个加载器和插件。

虽然已经有了 Webpack-cli，但这个最新版本有助于减少设置 Webpack 的工作量和复杂性。改进的目的是使突破变更的迁移变得更容易(同时仍然能够应用最佳实践)，并且具有允许定制的快速和简单的设置。

还有一个从 Webpack 1 到 2 的迁移功能，以及一个完整的附加系统，这样库作者就可以拥有自己的批准印章。详情请访问 webpack.webpack.cli

# 2017 年进展如何？

Webpack 通过投票系统(位于 webpack.js.org/vote)对社区中最受欢迎的改进进行排名。今年最值得期待的特性是:
-卷式提升
-更好的摇树
-硬盘缓存 Webpack 插件和原生实现
-通用类型脚本集成和支持(即编译、速度等。)
-使用不同的浏览器

Webpack 一直与 Firefox 在 web 程序集集成方面进行合作。该提案旨在使 web assembly 成为 webpack 中的一级模块类型。这一调整将消除开发人员对共享内存缓冲区或编译到 web 程序集模块的担忧。提议的两部分步骤流程指出，开发人员应该能够直接导入任何能够编译 web assembly 的语言，然后通过加载程序将其返回到 web assembly 模块，这样它就可以像 Javascript 一样使用。这应该允许 web assembly 的采用飞速发展，并增加开发人员的可访问性，他们可能无法在目前的状态下使用它。最初的提议可以在 http://bit.ly/2oubZqF 的[找到。](http://bit.ly/2oubZqF)

如果你有兴趣使用 Webpack 2 或成为赞助商，请访问[opencollective.com/webpack](//opencollective.com/webpack)。

要了解 Webpack 的最新动态或有任何问题，请在 Twitter 上联系 Sean Larkin[@ thelarkinn](https://dev.to/thelarkinn)。

作者 Trinh Kien & Necoline Hubner

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。