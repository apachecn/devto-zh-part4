# 如何创建自定义徽章来改进您的减价文档

> 原文：<https://dev.to/mlkrsrc/how-to-make-custom-badges-to-improve-your-markdown-documents-460k>

*封面照片由[凯文·卢亚特](https://unsplash.com/@luartevisual?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/badge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)* 拍摄

这篇文章是关于创建自定义徽章，类似于你在 Github 上的 README.md 上看到的最酷的项目。

你有没有想过创建一个自定义徽章或为什么他们是用于？人们使用它们来改善文档的外观并增加一些专业性。这很有用，尤其是对于开源项目。没有人想使用来自 Github repos 的代码，这些代码最后一次提交是在 2 年前(甚至是 6 个月前)，并且带有一个粗心的自述文件。这些徽章是展示你有多爱你的回购的一种方式。

我首先将它们用于我贡献的 react 组件的[回购](https://github.com/thundra-io/thundra-trace-chart)。我把这个 url `https://img.shields.io/npm/v/thundra-trace-chart.png`作为图片来源添加到 README 和 tada 的顶部！出现一个徽章 [![tada!](img/62ac8fa64d7720ee0ab66bb3692efac3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NUfjLOMr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/npm/v/thundra-trace-chart.png) ，显示组件的最新 npm 版本。最棒的是，当我们向 npm 注册中心发布组件的新版本时，它会自动增加版本号。

我追踪了这个魔法的来源，发现了这个不起眼的网站叫做 [shields.io](https://shields.io/) 。该网站有许多易于使用的徽章，用于最常见的用例，如我用于 npm 版本的包。然而，这对我来说还不够。我想做一个不被支持的自定义徽章(还没有！:)靠盾牌。它将被我工作了将近一年的项目的许多部分版本化所使用，这个项目叫做[桑德拉](https://thundra.io)。它是监控 AWS Lambda 功能的一个很好的工具，并且正处于初创阶段。因此，有时很难跟踪许多部分的版本(代理、层、插件、应用程序等)。)正在由团队夜以继日地开发。为了解决这个问题，让我们的文件更好一点，我想为桑德拉建立一个自定义的徽章，它看起来像这样；

[![alt text](img/250fc2c955a5cbfabe7d2b5dbf90111e.png "Custom Thundra badge")](https://res.cloudinary.com/practicaldev/image/fetch/s--PM61olP3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/endpoint.svg%3Furl%3Dhttps://thundra-version-0qw7bzmwj7wy.runkit.sh%26link%3Dhttps://thundra.io)

现在，我想简单地解释一下如何根据自己的口味制作一个。

### 从端点创建徽章

你可以用盾牌轻松创建简单的自定义徽章。例如，使用这个 url `https://img.shields.io/static/v1.svg?label=left&message=right&color=red`您会得到这样的结果； [![alt text](img/f3889394a1a8e6f828147e95a050a5fa.png "Custom badge")](https://res.cloudinary.com/practicaldev/image/fetch/s--ALTIZ9lt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/static/v1.svg%3Flabel%3Dleft%26message%3Dright%26color%3Dred) 但我无法使用 url 参数添加自定义图标。所以我发现从 [json 端点](https://shields.io/endpoint)制作定制徽章。

### 找到一个 SVG 图标

我需要一个 SVG 图标的徽章，幸运的是，我们有一个 SVG 版本的桑德拉图标，所以我用了它。这里的技巧是你需要去掉好的缩进，并且需要使 [svg 成为一个线性](https://dev.to/benjaminblack/using-a-string-of-svg-as-an-image-source-8mo)。我使用 Runkit 创建了一个简单的[端点](https://runkit.com/milkers/thundra-version)。然后，我将 Runkit 端点作为一个参数添加到一个通用 shields url 的末尾，就像这个`https://img.shields.io/endpoint.svg?url=https://thundra-version-0qw7bzmwj7wy.runkit.sh`一样，徽章的任务就完成了。

添加自定义徽章是显示您有多关心您的文档的好方法。给你的文档注入一些实时数据也很棒。如今，你不必仅仅因为一个版本的改变而更新你的文档，这些徽章会神奇地为你完成这项工作，让你的文档尽可能地保持新鲜。