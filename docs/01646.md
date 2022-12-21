# 静态站点生成器不适合我！我和杰基尔、雨果和奈特丽克斯的经历

> 原文：<https://dev.to/justaashir/static-site-generators-are-not-for-me-my-experience-with-jekyll-hugo-and-netlifycms-4mo5>

我的[上一个帖子](https://dev.to/justaashir/bulma-the-most-underrated-framework-of-the-css-framework-era-2gj8)是一个 banger，我不知道 Dev 的力量。我的下一篇文章在这里:

* * *

我的作家朋友(不懂代码)❤️希望我为他做一个网站，他想在网上上传他的故事，也希望他的作品可以在网上看到。所以每个人都知道最好的选择是什么？？ [Wordpress](https://www.wordpress.org/) ，嗯。它很容易工作，但我自己把它变得复杂了，我想跟上潮流，我听过很多次关于静态站点生成器的速度和效率，所以这是我的经验:

* * *

[Gatsby](https://www.gatsbyjs.org/) 是目前最流行的静态站点生成器，但它是基于 React 的，我还不知道任何 JS 框架，如果你没有很好地了解 [React](https://www.reactjs.org/) ，你就不能使用它。所以 [Gridsome](https://www.gridsome.org/) (VueJs)你也出局了。
_ _
[GitHub 支持的 Jekyll](https://jekyllrb.com) ，最古老的基于 Ruby 的静态站点生成器之一，学习它，不是最大的问题，但是你会卡在安装上。在浪费你大量时间安装它之后，Jekyll 中没有干净代码的原则。非常杂乱——不要再为哲基尔大惊小怪了，我们继续吧

* * *

Hugo 标记为“最简单的静态站点生成器”，它基于 GoLang。我找到了，它的安装比 Jekyll 容易多了。试着找一些好的教程，但是关于 Hugo 的资料不多，找到了这些:
那边 [Docs](https://gohugo.io/documentation/) 烂透了！我在那里没有找到任何答案！我陷入了许多问题，但我解决了它们！我决定和雨果一起工作。

* * *

下一步是学习制作雨果的定制主题！但是我在 YouTube 上没有找到一个视频是关于从头开始的整个过程的。我从 HugoThemes 下载了一个简单的主题，我试着定制它，我终于做到了。现在是应用 CMS 的时候了。我选择 [NetlifyCMS](https://www.netlifycms.org) 是因为它的名字里有 Netlify。所以我在 GitHub 上推送它，而在 Netlify 中，每次推送时我都为 Hugo 发出一个构建命令，但是什么鸭子！Git 子模块是针对主题的吗？我试图解决它，但没有找到任何答案，所以我把所有的/themes 文件复制到/layout 文件夹，这样它就成了默认样式。现在更多的问题来了？

* * *

在 Hugo 的所有这些和非常糟糕的文件路径系统之后，我给我的朋友提供了一个网站，他创建了新的帖子。然后他问我，他想改变网页的内容，还想自己制作更多的网页。我试过了，但是逻辑让我害怕！布局文件夹中没有可用于静态页面编辑的选项，还有许多其他选项！现在我不会回到雨果身边了。

* * *

医生们怎么了？他们这么狗屎？我只是想要一个静态的网站生成器来接受我的自定义主题，让它可以被 CMS 上的用户编辑它的每一部分。也许在《盖茨比》里有可能， [VuePress](https://vuepress.vuejs.org) 或者 [Gridsome](https://gridsome.org/) 但是我没有太多时间去学习这些框架。我是一名设计师。然后我还尝试了 [11ty](https://www.11ty.io) ，它让我兴奋，因为它直接使用了 Javascript。也有这么多可用的入门模板，但我已经在 Hugo 上浪费了我所有的时间，然后我回到最简单的老方法？？？？Wordpress。

你真的在我之前的帖子里给了我这么好的回应！就这些，在推特上关注我:[twitter.com/justaashir](https://twitter.com/justaashir)