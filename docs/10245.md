# 如何在 Linux 中安装和设置 Git

> 原文：<https://dev.to/xeroxism/how-to-install-and-setup-git-in-linux-27ie>

[![how to install git banner](img/45cf33ebcf9b3d9e0970069c408c6829.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nxw1FJsj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/fossnaija.com/wp-content/uploads/2019/08/git.jpg%3Ffit%3D665%252C394%26ssl%3D1)

## 什么是 git？

Git 是由 [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds) (是的， [Linux](https://dev.to/xeroxism/why-linux-works-d6b-temp-slug-8682900) 的创造者)创建的分布式代码修订软件。Git 用于项目/源代码版本控制。

这仅仅意味着它确保您可以在特定的时间点保存您的工作(任何[编码项目](https://fossnaija.com/wp-content/uploads/2017/08/fossnaija_logo.png))——版本。用 git 的话说，每次**保存**都被称为**提交**。

因此，您可以从已经完成的任何提交中加载项目。本质上，您能够控制您想要使用的项目的“版本”。

这种实践(提交代码)的一个优点是，在发生事故时——无论是由于开发缺陷还是错误，您都可以简单地加载项目的一个先前版本，而不是重新开始。

酷吧？

在使用 git 的所有特性之前，您必须[安装](https://fossnaija.com/installing-software-on-linux-using-gui/)并在您的 Linux 系统中设置它。所以让我们来看看我们该怎么做。

更新您的系统

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

安装 git 软件:

```
sudo apt-get install git 
```

Enter fullscreen mode Exit fullscreen mode

检查您刚刚安装的 git 的版本:

```
git –version 
```

Enter fullscreen mode Exit fullscreen mode

如果您正确安装了 git，您将得到类似于:
的输出

```
git version 2.3.2 
```

Enter fullscreen mode Exit fullscreen mode

## 第一次配置

既然已经安装了 gitinstalled，现在设置一些全局变量是很重要的。这些变量很重要，因为它有助于 git 识别谁对特定的代码库进行了更改。

它也将帮助开发者识别你是否会，也许在将来，你想要贡献给其他人的代码。最常见的身份识别形式是姓名(即用户名)和电子邮件。您可以像这样在 git 中设置这些配置:

```
git config -–global user.name “John”
git config –-global user.email “john-doe@email.com” 
```

Enter fullscreen mode Exit fullscreen mode

检查所有的配置值；

```
git config –list 
```

Enter fullscreen mode Exit fullscreen mode

记住，你应该关心的最重要的变量是用户名和电子邮件，尤其是在初学者阶段。也许以后当你想做一些高级的东西时，你可能已经熟悉了你可能需要的必要的配置设置。

## 当你需要帮助的时候！

要查看 git 子命令和语法的概述，您可以在终端上输入命令:

```
git help 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
git help -a 
```

Enter fullscreen mode Exit fullscreen mode

git 概念指南使用:

```
git help -g 
```

Enter fullscreen mode Exit fullscreen mode

要查看您可以用各种 git 命令(动作)或概念做什么，您可以简单地使用下面的格式。

```
git help "command"
git help "concept" 
```

Enter fullscreen mode Exit fullscreen mode

您可以在本地计算机上使用 git now 来跟踪项目/代码变更。

您可以下载 gitcheat-sheet 来学习您可以使用的最重要的 git 命令。

快乐的 Linux！

帖子[如何在 Linux 中安装和设置 Git](https://fossnaija.com/how-to-install-git-in-linux/)最早出现在 [Foss Naija](https://fossnaija.com) 上。