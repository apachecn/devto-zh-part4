# 面向企业的 Web 组件。第 2 部分:Nuxeo，Ionic，Vaadin

> 原文：<https://dev.to/webpadawan/web-components-for-enterprise-part-2-nuxeo-ionic-vaadin-22l7>

像前端生态系统中的几乎所有技术一样，Web 组件也有支持者和反对者。保罗·刘易斯在 Fronteers 今年演讲的[公告](https://twitter.com/FronteersConf/status/1146751390078918656)中提到了观点的极性。顺便说一句，我认为这是具有象征意义的，它将是 2011 年亚历克斯·罗素最初在[介绍](https://fronteers.nl/congres/2011/sessions/web-components-and-model-driven-views-alex-russell)Web 组件的同一场会议。

虽然我必须承认 Web 组件有优点和缺点，但我认为它们值得更深入的研究，在我看来，这里有一个我试图填补的空白。从我的经历和社区中观察到的情况成为我今年写的几篇博文的基础，包括这个“企业”系列，我仍然有一些要说的。

前一篇文章是关于企业规模的软件公司，非常关注所有权，做出正确的决策，并与网络标准保持同步。但是对于许多公司来说，软件开发不是核心领域，所以他们通常将软件开发外包出去，尤其是在构建供内部使用的 web 应用程序时。

创建这种企业级应用程序可能具有挑战性。它们通常是在预算紧张的情况下建造的，因为它们不会给所有者带来任何直接收入。同时，这些都是应用程序，用户可以在其中度过他们的整个工作日。最后，这样的应用程序往往有很长的生命周期，这意味着多年的维护、支持和痛苦的错误修复。

在这篇博文中，我想描述三家创建 UI 开发平台的公司，这些平台主要面向使用 Web 组件的企业客户。一个必要的免责声明:我是在 Vaadin 工作的前端开发人员，vaa din 是一家在此列出的公司——所以我试图通过提供比较技术的或多或少相等的表示来避免偏见。

## 努比亚语

Nuxeo 是一家提供企业内容管理[平台](https://github.com/nuxeo/nuxeo)的软件公司，该平台用 Java 编写，在 Apache 2.0 许可下开源开发。Nuxeo 声称“零遗产”和创新投资是其优势之一。在前端方面，Nuxeo 是 Web 组件规范的早期采用者之一这一事实可以证实这一点。

2014 年，谷歌在 Chrome 中实现了所谓的“v0”自定义元素和阴影 DOM 提议，并积极倡导它们。Nuxeo 属于那些对 Web 组件承诺的“使用平台”方法感到兴奋的公司。因此，Nuxeo 将这些前沿规格视为现有 JSF 堆栈的潜在替代品。

Nuxeo 平台的新客户端名为“Web UI 1.0”，于 2017 年推出[。它是用 Web 组件和 Polymer 1(使用 HTML 导入和“v0”提案)构建的。此外，](https://www.nuxeo.com/blog/everything-you-need-to-know-about-web-ui/) [nuxeo-elements](https://www.webcomponents.org/author/nuxeo) ，一个不断发展的 UI 和数据可视化控件集合，用于使用 nuxeo 构建 web 应用程序，已经以开源方式开发出来。

自从决定使用 Web 组件以来，Nuxeo 发生了很多变化。特别是，元素集合现在更新到聚合物 3，并移动到 [monorepo](https://github.com/nuxeo/nuxeo-elements) 。最后，JSF 已经在 LTS 2019 版本中被[弃用](https://www.nuxeo.com/blog/jsf-deprecated/)，并给出了积极的解释，一个帮助将现有的基于 JSF 的项目转换为 Web UI 的迁移工具已经在测试版中发布。

Nuxeo 做出的一个重要决定是[结束对 2018 年宣布的 IE11](https://www.nuxeo.com/blog/nuxeo-web-ui-ie11/) 的支持。正如我们可以理解的那样，根据“过多的时间”投入到修复 bug 和性能问题的措辞，这是一个经过大量斗争后的艰难决定。现实是 IE11 在转移到 Web 组件时需要备份解决方案(Nuxeo 的情况下是 JSF)。

## 离子型

[Ionic](https://ionicframework.com) 是一家软件公司，提供开源的麻省理工学院许可的跨平台 UI 开发工具包，名为 [Ionic framework](https://github.com/ionic-team/ionic) ，并提供商业服务和支持。最初专注于使用 Cordova 和 Angular 为移动设备构建原生风格的应用程序，Ionic 最终推出了自己的工具: [Stencil](https://github.com/ionic-team/stencil) ，一个 Web 组件和 PWAs 的编译器。

根据最近的[推文](https://twitter.com/manucorporat/status/1147438907342118912)，Ionic 和 Stencil 的核心开发由 10 个人负责，GitHub 上的[团队只有 15 个公共成员。然而，自 2013 年成立以来，Ionic 一直专注于打造一款面向企业使用的产品。这解释了选择 AngularJS 的原因，在 React 的兴起严重影响 JS 景观之前，AngularJS 曾经很流行。](https://github.com/orgs/ionic-team/people)

2017 年聚合物峰会上宣布的 Stencil alpha[用了近 2 年时间，于 2019 年 6 月登陆为](https://www.youtube.com/watch?v=UfD-k7aHkQE) [1.0.0](https://github.com/ionic-team/stencil/blob/master/CHANGELOG.md#%EF%B8%8F-100-2019-06-01) 稳定发布。Ionic 团队正在开发新功能，尤其是与 SSR 相关的功能，SSR 通常被认为是影子 DOM 的“缺点”之一。最近，模板团队分享了预渲染和客户端水合[演示](https://twitter.com/adamdbradley/status/1143554342122938368)，作为未来的改进。

关于 Stencil 的一个重要注意事项是，尽管它基于 Web 组件，但它将自定义元素视为编译目标。最重要的是，Stencil 提供了一套自认为是保持竞争力的关键创新功能:类型脚本、JSX、虚拟 DOM、反应式数据绑定和异步呈现(被描述为“类似于 React Fiber”)。

在 Max Lynch 最近的博客文章中，Ionic 被认为是 Web 组件两种对立观点的“中间点”。Stencil 提供的开发人员体验类似于 Angular(类型脚本、装饰器)和 React(生命周期回调、JSX)。此外，Ionic 跟随 JS 框架社区的趋势，比如构建[设计系统](https://blog.ionicframework.com/build-your-next-design-system-with-web-components/)。

## [我要求](#vaadin)

Vaadin 是一家提供框架、组件和工具来用 Java 构建 web 应用的公司。从历史上看，自 2009 年以来，Vaadin 一直使用 GWT，并将其用于 UI 组件。从 Java 编译到 JavaScript 在当时是一项关键功能，但在 2018 年，Vaadin 已经离开 GWT，并推出了基于 Web 组件标准的 UI 的[版本 10](https://vaadin.com/blog/but-why-web-components-) 。

Vaadin 被认为是聚合物领域的先驱之一。Web 组件的早期实验始于 2015 年的 Polymer 0.8，结果创建了 [Vaadin 元素](https://vaadin.com/blog/building-components-on-top-of-polymer):一组旨在无缝扩展聚合物元素目录的组件。最初的集合包含 5 个元素，专注于功能丰富的复杂控件，比如[数据网格](https://vaadin.com/blog/reinventing-the-data-grid)。

早期版本的 Vaadin Elements 过去依赖于纸张元素，并从中利用了很多，同时也是超越聚合物团队的一个例子，Rob Dodson 在“[build with Polymer](https://youtu.be/945pO9Yn1ns?t=408)”polycast 一集中提到了这一点。然而，随着时间的推移，对所有权，尤其是对代码库、版本和外观的完全控制的需求变得越来越明显。

实现自己主题的能力，不局限于材料设计，已经被证明是客户项目的基本特征。因此，我们用所有的原语扩展了 Vaadin 组件:按钮、文本字段、覆盖等。此外，我们想出了自己的[主题化方法](https://youtu.be/xSnG-mNWhqI)，以摆脱被抛弃的 CSS mixins 提议和纸张元素使用的`@apply`语法。

截至目前，瓦丁组件使用 [Lumo](https://demo.vaadin.com/lumo-editor/) 作为默认主题，并提供材料版本作为替代。组件集已经发展了，我们在改进的同时学到了很多，包括对聚合填充的贡献和处理浏览器错误。最后，我们的组件现在与 [Vaadin Flow](https://vaadin.com/flow) 集成，这是一个为 Java 用户提供 Web 组件支持的框架。

## 总结

内部企业 web 应用程序形成了一个独立的世界。它们很好地隐藏在 NDA 之下，充满了遗留问题，有时并没有真正跟上现代趋势，如微小的捆绑包大小——谁会关心在快速的公司内部网的桌面上下载 500 kB 的 JS 呢？但某些旨在接近这些应用的产品是用 Web 组件构建的。

Nuxeo、Ionic 和 Vaadin 是提供这类产品的公司。他们都提供长期支持(LTS)，要么作为高级功能( [Ionic](https://ionicframework.com/docs/intro/support#long-term-support-premium-) )，3 年支持( [Nuxeo](https://doc.nuxeo.com/nuxeo-release-cycle/) )或 5 年支持( [Vaadin](https://vaadin.com/roadmap) )。但是，除了人们可以筹集资金并在多年内保持网络平台稳定性的事实之外，让我概述一下我认为我们可以学到的其他一些经验:

1.  **灵活性**。一些喜欢函数式方法的人一直在抱怨定制元素被设计成基于类的。但是 DOM API 使用类语法和原型继承的事实不应该限制任何人实现自己的抽象层:[钩子](https://css-tricks.com/haunted-hooks-for-web-components/)，对象，甚至 DOM 树的服务器端表示。

2.  **主题性**。结合自定义 CSS 属性使用影子 DOM 进行主题化似乎是 web 平台的一个被低估的特性。大多数开发人员坚持使用全局 CSS 和类名约定，而在理论上，我们现在有了潜在的更具可伸缩性和可靠性的解决方案。“适者生存”在行动？

3.  **IE11:一场表演赛**。尽管 Web Components polyfills 支持 IE11，但几乎不可能确保在那个“[兼容性解决方案](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/The-perils-of-using-Internet-Explorer-as-your-default-browser/ba-p/331732)”中运行的大型应用程序的正确性能。要让一个应用程序在 IE11 中工作，这在企业环境中很常见，实际上意味着需要一个单独的版本(YouTube 就有一个)。

随着时间的推移，有望有更多的企业开始考虑 Web 组件，尤其是在 Firefox ESR 68 发布之后。在编写本报告时，它正在经历所谓的资格审查和测试阶段。从 Firefox 63 开始，对自定义元素和阴影 DOM 的支持将会面向更广泛的受众。

就我个人而言，我希望今年晚些时候本系列的下一篇博文会有足够的素材。这就是你，读者，能给我很大帮助的地方。如果你的公司正在使用 Web 组件，请在评论中联系我，并分享你所学到的！当然，也可以随意分享任何其他反馈，我真的很感谢它，它让我受到鼓舞。