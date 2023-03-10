# 准备您的开源项目

> 原文：<https://dev.to/zenika/preparing-your-project-being-open-sourced-5bdp>

在[之前的一篇文章](https://oss.zenika.com/white-paper/getting-started)中，我们帮助您提交了您的第一篇投稿。您现在想启动自己的开源项目，但不知道在让所有人查看您的代码之前该做些什么？别担心，在本文中，我们将为您提供启动项目所需的所有先决条件。

## 先决条件

如果你希望你的项目有贡献，你需要写最少的文档。有四个必不可少的文件:

*   自述文件
*   许可证
*   投稿指南
*   行动守则

我们将描述每一个，并为您提供指南来帮助您编写它们。

## 自述

第一个是众所周知的，你可能已经有一个了，但是让我们来谈谈 README.md。

Mike McQuaid 从理论上阐述了[开源贡献者漏斗](https://mikemcquaid.com/2018/08/14/the-open-source-contributor-funnel-why-people-dont-contribute-to-your-open-source-project/)以及如何让贡献者参与到你的开源项目中。您需要在自述文件中提供每种类型的访问者(*用户*、*贡献者*、*维护者*)的信息。

> 你的项目描述和你的自述文件的第一段应该是你的项目的一个简单的总结，它击中了人们搜索的所有重要关键词。安德烈·彼得罗夫

首先，没有人在你脑子里。你应该解释你项目的目标。它不一定是一篇论文，一句话就足够好，就像在[会议厅的自述](https://github.com/bpetetot/conference-hall)中一样。

> Conference Hall 是一个开放的 SaaS 平台，用于管理您的会议和集会的论文征集和演讲者提交。演讲者编写一次演讲，就可以提交给平台的每个事件。

这一部分是新人阅读你的项目的第一件事，你需要引起他们的注意。

自述文件中要记录的另一个重要部分是如何安装和使用您的项目。对于*用户*来说，能够方便地测试它是至关重要的。盖茨比很好地解释了如何用他们的命令行界面在 5 分钟内运行一个网站[。](https://github.com/gatsbyjs/gatsby#-get-up-and-running-in-5-minutes)

最后但同样重要的是，你应该为未来的贡献者提供指导。大多数时候，一个链接到你的投稿指南的部分就足够了。

makeareadme.com 上有一个[模板](https://www.makeareadme.com/#template-1)。它包含了我们讨论过的所有部分。你可以在这个网站上找到更多关于如何写好自述的信息。你也可以使用工具来生成你的自述文件，比如 [readme-md-generator](https://github.com/kefranabg/readme-md-generator) 。它用从 git 配置或您的`package.json`文件中提取的信息填充它，如果您的项目是用 JavaScript 创建的。你可以在 GitHub 上找到各种语言的[自述文件生成器](https://github.com/search?utf8=%E2%9C%93&q=generate+readme&type=Repositories)。

## 执照

许可证文件使开源成为可能。它通过给用户和*贡献者*使用、复制、修改和贡献你的项目的权利来保护他们。这个文件是强制性的，你应该考虑不要参与没有提供许可证的项目。

OSI 是一个 20 多年来一直致力于促进开源软件和社区的组织。他们有审查许可证的流程。OSI 批准的许可证是最受欢迎的，如[麻省理工学院](https://opensource.org/licenses/MIT)或[阿帕奇 2.0](https://opensource.org/licenses/Apache-2.0) 。你可以在[choosealicense.com](https://choosealicense.com/)上找到一个[比较好的](https://choosealicense.com/licenses/)执照。

## 投稿指南

> 开源存储库或站点中的 CONTRIBUTING.md 文件为潜在的项目贡献者提供了一个简短的指南，告诉他们如何帮助您的项目或研究小组。按照惯例，将单词“contributing”大写作为文件标题，并将其保存为 markdown 中的资源(因此扩展名为。md)。
> - [Mozilla 科学实验室](https://mozillascience.github.io/working-open-workshop/contributing/)

之前我们在自述文件中谈到了“开源贡献者漏斗”，我们提供了文档来帮助*用户*安装和使用您的项目，现在我们将关注*贡献者*。投稿指南旨在为每个想参与你的项目的人提供指导。

大多数情况下，贡献是由用户做出的，因为他们在使用您的项目时遇到了问题。有关于如何报告错误或建议新功能的说明是很好的。

如果你想要代码贡献者，你需要提供关于如何建立项目开发环境和如何提交贡献的所有细节。摩卡的投稿指南是一个很好的例子，一步一步的指导你把你的投稿合并起来。

投稿指南也是描述编码风格的合适地方。您可以强制实施良好的实践，比如测试或惯例，就像在 [Immutadot 的投稿指南](https://github.com/zenika-open-source/immutadot/blob/master/.github/CONTRIBUTING.md#tests-and-code-style-policeman)中一样。

如果你的项目在 GitHub 上，当一个贡献者打开一个问题或者创建一个 pull 请求时，这个文件会被自动链接到。

## 行为准则

> 在你的公共库中有一个 CODE_OF_CONDUCT.md 可以让潜在的*贡献者*提前知道他们将如何被社区和*维护者*对待。
> - [迈克尔·乔利](https://dev.to/michaeljolley/using-a-contributing-codeofconduct-to-assist-others-in-contributing-to-public-repositories-1l90)

你的项目的行为准则是一份保护每个参与者的文件。它有助于创造一个友好的社区。

对你的行为准则的一个很好的补充是解释你计划如何执行它。重要的是表明你认真对待此事，这样每个人都知道在违反行为准则的情况下会采取什么行动。

您还应该明确报告违规行为的方式，例如通过电子邮件。

最著名的行为准则是[贡献者契约](https://www.contributor-covenant.org/)。它被数以千计的开源项目所使用。如果你的项目在 GitHub 上，你可以通过你的库接口直接添加贡献者契约或者[公民行为准则](http://citizencodeofconduct.org/)。您需要转到`Insights > Community > Code of conduct`，然后点击添加按钮并选择行为准则，它将为您创建一个提交。

您的项目现在可以开源了。你可以开始推广它，并获得你的第一笔捐款！

如果你想了解更多关于我们项目的新闻或未来的文章，请关注我们的 twitter [@ZenikaOSS](https://twitter.com/ZenikaOSS) ！