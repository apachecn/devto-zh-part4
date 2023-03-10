# 面向企业的 Web 组件。第 1 部分:Salesforce、Oracle、SAP

> 原文：<https://dev.to/webpadawan/web-components-for-enterprise-part-1-salesforce-oracle-sap-e70>

Web 组件仍然是争论的主题。很自然的，现在当他们被所有常青浏览器支持的时候，有些人会问自己那是不是应该是“新时代”。然后事实证明，新标准并不是银弹，并不符合某些人的期望，并不是所有的 bug 都得到修复，web 平台的某些部分需要进一步改进。

我在以前的博文中很少描述这样的问题( [1](https://dev.to/webpadawan/beyond-the-polyfills-how-web-components-affect-us-today-3j0a) ， [2](https://dev.to/webpadawan/the-journey-of-web-components-wrong-ways-lacking-parts-and-promising-paths-1d5a) )。然而，某些对 JS 生态系统有一定影响的意见领袖倾向于批评整个想法，声称 Web 组件是基于错误的假设，没有实际用途。这些说法有其道理，但它们在社会上造成了许多误解。

您可能还没有意识到这一点，但事实上 Web 组件正在这个行业中最不活跃的部分——企业 UI 开发——经历着令人印象深刻的增长。大公司对长期解决方案感兴趣，其中一些公司在过去吸取了许多痛苦的教训。这就是为什么现在他们表达了对网络标准的强烈支持。

在本系列中，我将概述几个用于业务 web 应用程序开发的企业平台，它们采用 Web 组件作为编程模型的一部分。我们将看到随着时间的推移，他们如何在向 web 标准转变的同时引入变化，他们给开源生态系统带来了什么价值，以及我们可以从他们的经历中吸取什么教训。

## 销售队伍

Salesforce 是一家软件公司，提供 CRM 平台和一套企业网络应用程序。这些应用程序中有许多来自收购，这是市场扩张的常见情况，并且是使用不同的堆栈构建的。同时，客户可以使用 Salesforce 平台及其提供的工具构建自己的应用程序。

Kevin Schaaf 和 Caridy Patiñ在 Google I/O 2019 的演讲中[介绍了 Salesforce 的 Web 组件案例](https://youtu.be/YBwgkr_Sbx0?t=272)。Arthur Evans 在[的文章](https://web.dev/web-components-io-2019#enterprise:-web-components-at-salesforce)中列出了演讲的要点。Salesforce 选择 Web 组件的原因包括需要一个通用组件模型来确保一致的外观和感觉以及向后兼容性。

然而，该列表中还有其他有效的要点，我个人认为这些要点更重要，尤其是对专有解决方案的担忧、受传统技术束缚的风险以及对呆在封闭生态系统的围墙花园中的恐惧。这就是“不是这里发明的”症状如何在快速变化的前端世界中清楚地表现出来。

2018 年底，Salesforce [宣布](https://developer.salesforce.com/blogs/2018/12/introducing-lightning-web-components.html)Lightning Web Components 作为平台中 UI 开发的新组件模型，利用了 Web 标准的优势。特别关注的是与以前的模型 Aura 组件的共存和无缝互操作性，并建议考虑随着时间的推移迁移到 Lightning。

几个月后，Salesforce [开源了](https://developer.salesforce.com/blogs/2019/05/introducing-lightning-web-components-open-source.html)的 Lightning Web 组件框架，并展示了麻省理工学院授权的[库](https://github.com/salesforce/lwc)，新的[网站](https://lwc.dev)和`lwc-create-app` CLI 工具，允许创建新项目。Salesforce 开发人员传道者也在撰写关于相关前端技术的博客文章，比如用 Jest 和 debugging 对组件进行单元测试。

## 甲骨文

Oracle 是一家提供各种企业软件产品的公司，包括客户端 web 应用程序。这些应用程序中有许多是用 T2 Oracle JET T3 或 JavaScript Extension Toolkit 构建的，可以在开源 UPL 许可下获得。注意单词“toolkit”代表并强调了模块化结构:JET 不是一个框架，而是一组库。

2017 年，Oracle [在 JET 4.0 版本中引入了对自定义元素的](https://blogs.oracle.com/developers/announcing-oracle-jet-40-and-web-components)支持。这个决定是为了更好地符合 HTML 标准和现代规范。与此同时，JET UI 组件的新架构以更好的语法、更直观和更自然的方式呈现给了 UI 设计人员和开发人员。

在 JET 3 . 2 . 0 版本之前，所有组件都被包装成 jQuery UI 小部件。实施了新的“语法”来与现有模型并行工作，并且在升级到新版本时明确表示不需要将[迁移到新模型。但是所有的新特性、开发者指南更新等都只针对新的 web 组件。](https://www.oracle.com/webfolder/technetwork/jet-400/globalSupport-releaseNotes.html#upgrade-custom-elements)

注意，放弃 jQuery 并不是使用定制元素的动机。经过两年的时间和 3 个主要版本的大变革，Oracle JET 仍然在使用 jQuery，以及其他不那么花哨的库，如 RequireJS 和 Knockout(在 FAQ 中甚至有一个“ [Why Knockout](https://www.oracle.com/webfolder/technetwork/jet/globalSupport-FAQ.html#why_knockout) ”部分)。JET 中从 Sass 到 CSS 自定义属性的[转换](https://www.oracle.com/webfolder/technetwork/jet-700/globalSupport-releaseNotes.html#theme_changes)也很慢。

这是我们可以学到的下一课:在企业范围内，渐进迁移是必须的。大公司负担不起从头重写一切，同时必须维持项目数年。Oracle JET 似乎在传统和技术更新之间保持了合理的平衡，同时也考虑了生态系统，并对未来有自己的[愿景](https://medium.com/@jtiago/building-the-future-of-oracle-jet-ecosystem-b9bde34cc88d)。

## SAP

SAP 是一家提供包括 ERP 系统在内的企业软件的公司。对于前端开发者，SAP 提供了在 Apache 2.0 开源许可下可用的 [OpenUI5](https://github.com/SAP/openui5) 框架。UI5 被定位为企业级 JavaScript 工具包，用于构建运行在几乎任何浏览器上的 web 应用程序，使用 jQuery 作为其基础并遵循 web 标准。

2019 年初，SAP [宣布](https://blogs.sap.com/2019/02/11/ui5-web-components-the-beta-is-there)UI5 Web 组件库的测试版，作为 UI5 演进项目的关键支柱，旨在实现 UI 元素的独立消费。另一篇[博客文章](https://blogs.sap.com/2019/01/16/ui5-and-webcomponents-working-together/)显示，SAP 开发人员从 2014 年开始评估 Web 组件。现在他们看起来非常乐观:“时间到了，网络准备好了！”

在最初发布后的几个月，在撰写本文时, [RC1](https://github.com/SAP/ui5-webcomponents/releases/tag/v1.0.0-rc.1) 已经及时发布，将在 UI5 大会上展示。除了与实际开发保持同步，SAP 还发布了一个带有互动游乐场的[网站](https://sap.github.io/ui5-webcomponents/)，一个关于如何开始的[教程](https://blogs.sap.com/2019/04/01/the-fastest-way-to-get-started-with-ui5-web-components/)，几个演示应用和一个由 UI5 首席架构师主持的[新闻广播](https://open.sap.com/static/ui5-newscast/?name=2019-03-05_ui5newscast008.mp3)片段。

关于 UI5 Web 组件的一个重要注意事项是，与作为现有产品的 OpenUI5 相比，它们是如何定位的。有一个特殊的[部分](https://github.com/SAP/ui5-webcomponents#classification-of-ui5-web-components)将新组件描述为“补充产品”，而不是 OpenUI5 的继任者。我们可以从这里学到的经验是，在架构更新的同时，清楚地表达信息是非常重要的。

还有一件事，与上面的观点有关:摆脱 jQuery。我们又来了:有时架构师必须向用户解释他们所做的决定。我强烈推荐阅读 SAP 的 Andreas Kunz 的评论,以了解大公司对他们使用的工具的实际价值和他们做出的决策的影响的考虑有多仔细。

## 总结

Web 组件最近获得了广泛的浏览器支持:2018 年 10 月 Firefox 63 中发布了自定义元素和 Shadow DOM，基于 Chromium 的 Edge 现在可以在 [canary 和 dev channel](https://www.microsoftedgeinsider.com/en-us/download/) 中使用。然而，某些企业已经选择 Web 组件作为他们 UI 开发平台的基础。

Salesforce、Oracle 和 SAP 就是这类公司的例子。他们的前端解决方案是使用不同的方法创建的，并且他们都处于采用新组件模型的不同阶段。比较这些并不是这篇博文的主要目的，所以让我概述并简要阐述我认为我们应该从他们的成就中吸取的教训:

1.  **所有权**。相比专有解决方案，更倾向于 web 标准是为了避免供应商锁定或陷入封闭的生态系统。关注 web 标准可以降低新开发人员的准入门槛，同时还可以提供对技术堆栈的完全控制，并促进内部能力的增长。

2.  **逐渐迁移**。能够将 Web 组件与其他库相结合是确保两种开发模式之间平稳过渡的关键。最初作为“补充解决方案”或“替代语法”引入的新方法，必须不断发展并经受时间的考验，才能最终取代旧模式。

3.  **决策影响**。大公司在升级技术栈时会仔细评估他们的选择，这对于快速发展的 JavaScript 生态系统尤其重要。在企业 UI 开发中，任何新特性都要经历一个漫长的验证阶段，这就是 web 标准发挥作用的地方。

在本系列的第二部分中，我将从一个稍微不同的角度继续概述用 Web 组件构建的前端工具包。我们将探索三种不同的开源平台，它们本身作为产品提供，伴随着商业服务——并希望从中学习一些其他的经验。敬请期待！