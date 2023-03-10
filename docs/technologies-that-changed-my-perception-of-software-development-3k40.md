# REST 速成班，GraphQL 和 Graphback #1:简介

> 原文：<https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-3k40>

* * *

# 

<center>简介</center>

* * *

大家好，我叫 Michal，是沃特福德理工学院信息技术专业的三年级学生。作为一个有一个小宝宝和繁忙的家庭生活的成年人，回到大学不是一个容易的决定，但是嘿！我做到了，我认为这是我做过的最好的决定之一！从我记事起，我就对技术感兴趣，然而，我只是在大学二年级时才意识到我绝对热爱软件开发。IT 专业的理学学士学位非常有趣，它能让你对它有一个广泛的理解，然而，它并没有深入到编程/软件开发中，虽然我认为我已经准备好接受任何软件开发工作了...我错了！
只有当我发现 Red Hat 正在招聘暑期实习生时，我才意识到软件开发到底是什么，但让我们把我的 Red Hat 实习故事留给另一篇帖子，并专注于我希望这篇帖子是什么，这是一篇非常简短的介绍:

*   [休息](https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-34lm)
*   [图表 QL](https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-mbm)
*   [Graphback](https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-4817)

由于这些技术让我看到了大量的可能性，我们将使用 Express 构建一个简单的 RESTful Node.js 服务器，然后使用 Apollo Server Express 迁移到 GraphQL，然后展示 Graphback 的魔力！

### 先决条件

我认为除了一些基本的 Javascript 知识，熟悉数组如何工作以及如何与数组交互，构建基本的对象之外，你真的不需要太多，剩下的我们会介绍！

*   对客户端-服务器架构的基本理解([客户端-服务器架构](https://www.britannica.com/technology/client-server-architecture)
*   JavaScript -基础知识
*   NodeJS -对 Node 的基本理解，但不是必需的( [NodeJS 教程](https://www.tutorialspoint.com/nodejs/index.htm))
*   命令行-基本理解

### 设置完毕！

首先，我们需要得到我们的代码编辑器，我强烈推荐使用 [Visual Studio 代码](https://code.visualstudio.com/download),因为它简单且轻量级。这是我将要使用的，如果你在屏幕上看到的与截图相符，那就更容易理解了！

接下来，因为我们要使用 Node.js，请从[这里](https://nodejs.org/en/download/)下载

我们将使用 npm 作为包管理器，它由一个命令行客户端(也称为 npm)和一个公共和付费私有包的在线数据库(称为 npm registry)组成。由于我们的项目将需要安装依赖项，我们需要一些东西来管理它——NPM 将为我们做这件事！Npm 也是 Node 的默认包管理器，所以我们还不需要对它做任何事情。

现在应该就是这样了！先从[休息开始吧！](https://dev.to/aerogear/technologies-that-changed-my-perception-of-software-development-34lm)