# 我如何和 Jekyll 一起写了我的第一个博客，为什么

> 原文：<https://dev.to/luturol/how-i-made-my-first-blog-with-jekyll-and-why-52i0>

你可以在[我的个人博客](https://luturol.github.io/jekyll/how-i-made-my-first-blog-with-jekyll)中准备好原文

## 动机

之后想做个博客和哥哥讨论一下。我得出的结论是，有必要开一个博客来学习更多关于编程的知识，并教那些可能和我一样在这个问题上苦苦挣扎的人。现在，我将创建一系列关于 Jekyll，Liquid templates 和 Css 的帖子，并修改我的博客，让更多的我出现在上面。在 Henry Quinn @ quincuatro 关于 [dev.to](http://dev.to) 的一篇关于 [DevOps 的文章中，我对在公共场合学习产生了浓厚的兴趣。](https://dev.to/quinncuatro/learning-devops-in-public-c26)

## 什么是当众学习？

向所有人展示你在那一刻正在学习或发展的东西。为什么？因为，当你教别人的时候，你最终会学到更多的东西，并在相同的主题上帮助别人。它有助于你记忆和练习这个主题。

## 当众学习的好处

当你学习的时候，你会帮助别人不要像你一样挣扎，你会练习写日记，也许会学另一种语言(我的情况)。

## 开始吧

首先，我在这个系列中要写的是 CSS，Liquid Template 和 Jekyll，因为这是我用来建立个人博客的。最后随时给我一个反馈，给我更多的帮助。

### 为什么是哲基尔？

我不想在任何地方托管我的博客，也不想在基础设施和安全性方面有成本和问题，我真的只想专注于简单快速的编程，所以我最终选择了 Jekyll(Github Pages 教程推荐的)。

Jekyll 是一个基于特定模板的静态站点生成器。

它是用 Ruby 创建的，所以你必须在你的电脑上安装。在 Jekyll 的网站上有安装 Ruby 和 Jekyll 的教程。

要初始化您的项目，您必须执行以下命令:

```
jekyll new nome-do-blog 
```

它将在自己的文件夹中创建您的项目结构。

Jekyll 默认是使用最小模板。要找到文件的位置，只需使用下面的命令:

```
bundle show minimal 
```

它会显示它的目录。

如果您想要更改模板并在版本控制程序中使用，那么您应该将文件复制到项目目录中。

建立和执行你的网站从来没有那么容易！！仅在您的项目主文件夹中执行以下命令。

```
bundle exec jekyll build 
```

建成后:

```
bundle exec jekyll s 
```

现在你有了自己的博客，在 localhost:4000 上使用了最少的模板。

随着我了解的越来越多，并修改我的个人博客以形成我自己的风格，我会发更多的帖子并解释它。

这是所有的乡亲。

别忘了喝水。