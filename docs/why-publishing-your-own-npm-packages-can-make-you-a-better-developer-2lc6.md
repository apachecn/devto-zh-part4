# 为什么发布你自己的 npm 包可以让你成为更好的开发者

> 原文：<https://dev.to/thegeoffstevens/why-publishing-your-own-npm-packages-can-make-you-a-better-developer-2lc6>

关于*如何*发布自己的`npm`包有很多很棒的资源，但是我想分享一些关于*为什么*你应该发布自己的`npm`包以及为什么这会让你成为更好的开发者的想法。

如果你正在寻找构建`npm`包的技术概述，Dan Vega 写了一篇关于发布你的第一个`npm`包的特别全面的文章。我强烈建议任何初学者阅读丹的指南。

[![therealdanvega](img/3a52f69163536a895b0e1be9f4a49d77.png)](/therealdanvega) [## 创建您的第一个 npm 包

### 丹·维加 1912 年 2 月 12 日 17 分钟阅读

#node #npm #javascript](/therealdanvega/creating-your-first-npm-package-2ehf)

## 首先，什么是`npm`包？

`npm`是一个工具，它为 Node.js 和 JavaScript 开发人员提供了一种快速简单的方法来安装、共享和管理`npm`包。`npm`包是由社区创建的模块，帮助开发人员极大地扩展他们的 JavaScript 应用程序的功能。

例如，流行的[请求](https://www.npmjs.com/package/request)包通过将处理所有复杂的`http`调用所需的函数简化为易于使用的方法，使开发人员能够进行`http`调用。

`npm`软件包通常由 [npm 公司](https://www.npmjs.com/about)通过其 npm 注册中心托管。npm registry 充当 Node.js、前端 web 应用程序、移动应用程序、机器人和 JavaScript 社区的许多其他需求的开源代码包的公共集合。

[![npm website screenshot: "build amazing things"](img/0a2b6bf3f27135318a76d80c1786dc55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dW53ZT_i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9w2isgu5pn9bi5k59eto.png)

有趣的是，使用 npm 包是为 Visual Studio 代码构建扩展的关键部分。作为 VS 代码扩展 [Code Time](https://marketplace.visualstudio.com/items?itemName=softwaredotcom.swdc-vscode) 的创造者，我们从充满活力的 JavaScript 生态系统中受益匪浅。我们最近也开始构建自己的一些软件包——我们的最新产品 [cody-music](https://www.npmjs.com/package/cody-music) 是一个音乐模块，可以控制用户的 Spotify 和 iTunes 应用程序。

在构建我们自己的 npm 包和 Visual Studio 代码扩展的过程中，我们学到了很多关于如何将 npm 包集成到开发过程中，以及为什么发布我们自己的包使我们成为更好的开发人员。下面是我们学到的一些经验。

### 轻松展示您的开发者技能

开发人员是建设者，不断地开始新的项目，尝试新的技术，并为社区倡议做出贡献。对于许多开发人员来说，建立一个投资组合是展示他们随着时间的推移而磨练出来的技能和能力的重要方式。

在他们的职业生涯中，开发人员很可能从事与其他项目交织在一起的项目。然而，npm 包可以是你工作的独立片段，你可以快速方便地与同事、雇主和朋友分享。

[![User profile on the npm registry](img/e0270429420b21fae9e8ef040cb1cafb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H8U-PkvH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/91k3j6dplnrjhtp5cdtz.png)

构建自己的 npm 包并与社区共享是展示您的 JavaScript 和编程技能的好方法。因为 npm 包可以像你希望的那样复杂或简单，所以作为开发人员，你可以利用你的全部知识。

你不仅能够展示你的编程技能，还将有机会展示你编写易于阅读和理解的文档的能力。npm 包对开发人员社区是公开可用的，因此您将负责打包您的模块供其他开发人员使用。

对于各种背景的开发人员来说，沟通是一项无价的技能。

### 学习如何建造别人可以使用的东西

我的许多个人兼职项目都是意大利面条式代码的可怕例子。给自己写代码的时候，很容易偷工减料，养成坏习惯。如果我在一段时间没有看一个项目之后重访它，甚至我自己的代码对我来说也是难以辨认的。

构建一个世界上任何开发人员都可以使用的东西是一个难得而又强大的机会。权力越大，责任越大。

[![npm package stats](img/c61bb7ee26dd0368e262c2d1c09c3cb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h9UbwI4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y8um70vvpvwhqpk4sbeb.png)

当其他开发人员发现您的项目时，您将需要学习如何编写清晰简洁的文档。您还需要在 GitHub 或其他存储库托管服务上组织您的项目，以便任何潜在的贡献者或好奇的开发人员能够仔细阅读您的代码。您将考虑如何最好地命名您的函数，以便其他人能够理解它们是如何工作的。

通过建立一个 npm，你将穿着另一个开发者的鞋子走一英里。你会开始明白别人会如何看待你的项目。在这个过程中，你会成为一个更聪明的开发者，更好地适应你周围社区的需求。

### 学习如何将一个软件项目从构思到发布

希望获得项目所有权的个人开发者将从设计他们自己的 npm 包中学到很多。构建 npm 包需要你监督整个产品开发周期，从构思到公开发布等等。

在这个周期的每一点，你都将面临新的挑战和独特的学习机会。您将处理从架构规划到需求审查的所有事情。你成为自己迷你产品的经理。

npm 封装提供了规模和能力的完美结合。构建一个 npm 并不复杂，也不会让你无法完成，但是最终的产品将有可能帮助整个开发者社区。

### 回馈开源社区

开源软件是软件开发世界的命脉。即便如此，为开源项目做贡献通常是令人生畏的，尤其是对初学者而言。

找到一个你想为之做出贡献的项目，仔细阅读贡献指南，提交 pull 请求供维护人员审查——整个过程有时会让人觉得有点不知所措。

如果你正在寻找一种更简单的方式来回报开源社区，发布一个 npm 包对初学者和有经验的开发者来说都是一个很好的机会。npm 包可以像你希望的那样简单或复杂。没有一个项目小到不能产生影响。

[![npm ecosystem at a glance](img/92dc5f75b01fad5c09f5abf800878188.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9qkNQvRy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0svth2pld17bws8a3fbu.png)

JavaScript 是最受欢迎的编程语言，因此您会发现许多有助于您学习的资源。有了不断发展的 JavaScript 工具生态系统的支持，您应该有信心在社区中有所作为。

### 成为社区的一部分，获得有价值的反馈

一旦你的 npm 包公开发布，其他开发者很可能会有反馈或建议。理想情况下，一些开发人员甚至会在 GitHub 上向您的项目提交 pull 请求。

与开发社区互动并学习如何整合反馈将有助于您磨练您的领导能力和项目管理技能。

[![Moment package on GitHub](img/cdff4ad9cc9fe22465078c77e616bcc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jOCUwJXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nd5pcmzgmfeoeo1injwy.png)

来自其他开发人员的建议也将帮助您成为一名更好的开发人员，让您接触到新的更好的开发实践或工具，然后您可以将这些实践或工具应用到您正在从事的任何其他项目中。拉动式请求审查和问题讨论是向社区学习的绝佳机会。

### 模块化你的代码以获得更干净的代码库和更好的测试

应用程序架构会很快变得混乱。毫不奇怪，为复杂的代码编写测试也很复杂。代码通常包含许多移动的部分，这些部分会被不知情的开发人员以不可预见的方式纠缠在一起。

提取部分代码并将其组织到模块化 npm 包中，将使您的代码库变得更加清晰，并帮助您在应用程序的不同部分更好地利用可重用代码。团队成员之间可以快速共享代码，并且可以消除功能的冗余实例。结果是一个更干净和更易于管理的代码库。

模块化代码也支持模块化测试。通过将代码分成包，您可以编写和执行可重复的测试，以确保每个包都按预期运行。大多数 npm 包都是这样构建的，任何人都可以安装和运行包中包含的测试。作为一名开发人员，你可以通过保持测试和原始代码之间的紧密联系来编写更好的测试，正如 npm 包要求你做的那样。

使用 npm 包来抽象应用程序的可重用功能，您将向代码中添加结构和组织，这将简化您的开发过程。

## 见 npm 包的威力

创建和发布自己的 npm 对于任何技能水平的开发人员来说都是一次有益的经历。通过学习如何构建自己的开源产品并管理其向开发人员社区的发布，您将增强您的开发人员技能。

对于开发团队来说，将代码模块化到单独的包中可以通过标准化管理代码所需的一些过程来加速开发。npm 包简化了整个构建、测试和部署工作流——这对于您的团队在整个代码库中使用和重用以及整个开源社区来说都是一大好处。

不要错过我们的代码编辑器插件[代码时间](https://marketplace.visualstudio.com/items?itemName=softwaredotcom.swdc-vscode)和我们的开发者简讯 [SRC](https://www.software.com/src) 。感谢阅读！