# NP kill——轻松、有风格地删除 node_modules 的解决方案

> 原文：<https://dev.to/carlillo/npkill-the-solution-to-deleting-nodemodules-easily-with-style-1o70>

# 简介

我与软件开发相关的最快乐的记忆之一是当我开发并在我的朋友和家人之间分发我的第一个项目时。虽然我在那之前很久就学会了开发，但那是在 2003 年我用汇编语言开发经典游戏 [Arkanoid](https://en.wikipedia.org/wiki/Arkanoid) 的时候。当然，这个游戏不能在网上发行，因为我们家里没有互联网，GitHub 也不存在，而且，在我的特殊情况下，我不知道任何面向开发者的社交网络。今天，这个故事是储存在我脑海中的美好记忆...可能在旧软盘里，保存在我父母房子里的旧盒子里。

现在，我教开发人员，我不断鼓励我的学生让他们所有的项目可见，并依靠社区，因为在那里他们可以找到最好的同事，在教室之外。如果发生在今天，我之前讲的故事会有很大的不同，因为我会做的第一件事是在所有社交网络上发布我的项目，并找到一个好的导师向她/他学习。

虽然你不能时光倒流，但我还是很高兴地呈现一个我以前的两个职业培训学生开发的项目，发表在 GitHub 上。我认为这是对社区的巨大贡献。

这个工具解决了我们在项目中安装大量依赖项时 us *node.js* 开发人员遇到的一个传统问题。随着我们开发更多的项目，我们计算机中的*节点模块*文件夹的数量也在增长。
很多时候，我们不记得 *node_modules* 文件夹存放在哪里，所以很难找到并删除它们。

[![node_modules_meme](img/90f6714a34c54a848fae2ee27711c6ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nBpQaViV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1198/1%2ADzW3ZKDzODwEawxzXSzimg.png)

这两个学生(现在是开发人员)开发了一个 *npm* 包，允许管理我们又旧又重的 *node_modules* 文件夹。因此，我非常高兴地宣布 [**npkill**](https://github.com/voidcosmos/npkill) 。

[![npkill](img/e6fb3a3610f3ba37d6b74da5a4d51011.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C6Q3rghh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/7935/1%2AGOk6v43CCwpxn2HJGlDEKg.png)

# [npkill](https://zaldih.tk/npkill/)

这个工具允许您列出系统中的任何 *node_modules* 目录，以及它们占用的空间。然后，您可以选择要抹掉的内容以释放空间。耶！

在解释获得这个重要工具的最简单的步骤之前，我将展示一个 GIF，其中我在我的笔记本电脑上使用这个工具保存了超过 1 GiB 的工作文件夹。

[![ezgif.com-optimize](img/9a3c507ee3aaf90e13df5eaa0b46f374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0HNMzWd3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://miro.medium.com/max/1999/1%2A-_K3MQpxrodwOsOrI4bRLg.gif)

### 安装

```
$ npm i -g npkill 
```

### 用法

```
npkill 
```

默认情况下，npkill 将从执行`npkill`命令的路径开始扫描*节点模块*。

用`↓` `↑`在列出的文件夹之间移动，用`Del`删除选中的文件夹。

要退出，`Ctrl` + `c`。

#### 选项

[![options](img/528807f236f5d6ec7ee8c2610fae1263.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P4LLT6Js--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1003/1%2AYBh1kCg37EO6T_HU3rVfVw.png)

**警告:** *在未来版本中，一些命令可能会改变*

### 例子

*   在您的*项目*目录中搜索**节点 _ 模块**目录:

```
npkill -d ~/projects

# other alternative:
cd ~/projects
npkill 
```

*   自动删除所有潜入您备份的**节点模块**:

```
npkill -d ~/backups/ --delete-all 
```

# 结论

在这篇文章中，我向你展示了两个重要的主题:

1.  这个神奇的工具将非常有助于管理我们的*节点模块*。
2.  你也可以开发一个很棒的项目。几个月前，他们在课堂上发现了一些基本概念，如“承诺”、“提升”、“设计模式”或“工具”。因此，记住你也能做任何事情。

我的建议是访问 [npkill 官方回购](https://github.com/voidcosmos/npkill)，阅读代码并开始帮助他们创建开源内容的有趣冒险。

### 越来越多，越来越多...

*   [npkill GitHub](https://github.com/voidcosmos/npkill)
*   [npkill Web](https://zaldih.tk/npkill/)

* * *

*最初发布于[https://carloscaballero . io](https://carloscaballero.io/how-to-reach-your-goals-1000-github-stars-in-the-first-open-source-software)。*