# Katalon Notes 插件

> 原文：<https://dev.to/minhthanh3145/katalon-notes-plug-in-3i2b>

## 记笔记

我对个人发展着迷了一段时间。这种魅力导致了许多激动人心的跌宕起伏的冒险，我也学到了一些宝贵的经验。其中之一是记笔记的行为。我看书的时候会做笔记，我做笔记是为了工作记录一些琐碎的事实，我不一定要经常用，但是以后可能要查阅。除了记笔记，任务管理也是必不可少的，这样我的生活就不会混乱，个人发展的时间也不会被不必要的占用。

但是回到记笔记，超时记笔记的行为相当于建立知识库的行为。我获得的知识基础基本上跨越了我的一生，它帮助我过上了更好的生活，这一切都源于记笔记。然而，它并不总是被许多人认为是有价值的，原因有很多，其中之一是他们还没有发现有必要形成这样的习惯。

## 对我的所作所为有了这种执念

为了在 Katalon Studio 的环境中促进这种记笔记的习惯，我决定实现一个简单的笔记插件，名为 **Katalon Notes** 。插件的链接在这篇文章的末尾。我个人使用的 [Trillium Notes](https://github.com/zadam/trilium) 是一个基于电子笔记的应用程序，作为记笔记的主要工具。对这个工具的痴迷极大地启发了我设计和实现 Katalon Notes。

无私的动机是，首先，它将为那些碰巧和我有共同兴趣的大量 Katalon 用户提供一种将他们的生产习惯融入 Katalon Studio 的方法(从而更多地使用 Katalon Studio LOL)。第二，没有人要求这样做，但是我的想法是让你的团队成员集体做笔记是个好主意。为了方便起见，Katalon Notes **将注释保存到本地数据库的项目文件夹**中，这样当您提交代码时，您也可以提交数据库文件，注释将被共享。

对于笔记应用程序来说，**自动保存更改**是很重要的，Katalon Notes 提供了这一功能。您可能想知道为什么这样一个特定的特性是可取的？因为记笔记的行为，当它最有意义的时候，包括回顾你关于特定主题的旧笔记，检索知识并在此基础上建立新的综合知识。能够做到这一点而不被灌输害怕失去写了一半的东西是一种福气，应该也是一种常识。

目前，Katalon Notes **支持 Markdown 语法**，这样你的笔记就可以有一个样式化的结构。一个限制是**还不能显示图像，**但是**我计划实现它**。

[![](img/eabc7ebf2d5d9edaf5ba733d1f2055be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QHYjQ2-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mwudx2krrk2q7jzgs0ay.png)

## 自顾自地，

设计和实现 Katalon Notes 对我来说是一个有趣的技术挑战，而且由于许多功能的可能性，它将继续这样下去。我发现自己在思考设计决策、代码重构决策和我经常在工作中必须做出的决策，伴随着个人项目带来的完全主人翁感。我可能会把我的想法写在博客上，因为写作是我的另一个习惯。

## 在哪里可以试出来

所以，说够了，即使我对自己感到恶心，这里是链接:

[https://store.katalon.com/product/105/Katalon-Notes](https://store.katalon.com/product/105/Katalon-Notes)

这个项目在这里开源:
[https://github.com/minhthanh3145/katalon-notes](https://github.com/minhthanh3145/katalon-notes)

## 什么是卡塔龙工作室

Katalon 是我目前工作的自动化软件测试公司/产品。