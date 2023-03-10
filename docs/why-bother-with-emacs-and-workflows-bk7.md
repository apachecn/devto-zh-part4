# 为什么要为 Emacs 和工作流费心呢？

> 原文：<https://dev.to/shrysr/why-bother-with-emacs-and-workflows-bk7>

我已经写了几篇关于提高生产力的不同方法和工具的文章，可能还有很多关于 Emacs 的。我的背景是计算物理，而不是编程，然而 Emacs 在过去的 3 年里一直是我日常工作流程中不可或缺的驱动力。

事实是，了解 Emacs(或 Vim)或拥有一个定制配置[并不是一个广受欢迎的技能](https://www.reddit.com/r/emacs/comments/9ghpb4/was_anyone_ever_impressed_by_your_emacs_skills/)，也不是实现惊人结果的必要条件。Emacs 配置适合个人工作流和风格，这可能是另一个人特有的边界。这种对定制工具的依赖也会大大降低你使用新 IDE 或文本编辑器的速度。

那么:为什么要把 Emacs 添加到不断增长的待办事项列表中呢？考虑到对“文本编辑器”的掌握不是你可以自由谈论的事情，也不是你经常期待感同身受的回应或者甚至是火花般的联系，这个问题就更加切题了。Emacs 会被许多人认为是一个深奥和古老的软件，有一个陡峭的学习曲线，对初学者不友好。

然而…..

[这篇文章](https://blog.fugue.co/2015-11-11-guide-to-emacs.html)阐述了 Emacs 可以帮助 PHB(尖头发老板)的许多方面。互联网上充斥着[几个](https://news.ycombinator.com/item?id=11386590)T4 关于组织模式和 Emacs 如何让生活变得更好的例子。这是 Howard Abrams 写的另一篇很酷的文章,关于使用 Emacs 作为他的(唯一的)窗口管理器，代替桌面环境。

观看一个有经验的人处理他的工具强调了它背后潜在的艺术形式，尤其是与一个业余爱好者的笨拙相比。是的，如果有足够的时间，业余爱好者可以完成工作，并且根据他的能力，甚至可以达到有经验的专业人士的水平(最终)。

然而，随着经验的积累，实现最佳结果的工作流程和步骤变得更加清晰。我经历了一种指数级增长的迫切需求，即实现特定的偏好，以更快、更少的错误获得所需的优化结果。

因此，很明显，所使用的工作流程和工具必须允许条款的发展、定制和自动化。对于数据科学和编程领域来说尤其如此。在定制方面，我认为没有什么比 Emacs 更好的了。

想象以下情况:

*   拥有不同语言的脚本或代码片段的组合来快速启动一个项目，只需按几个键就可以实现？
*   用一个单独的文档维护一个博客，文章在状态改变时自动更新。(奥克斯-雨果)
*   在单个文档中使用多个 R 环境。(Org-babel，ESS) <sup>[2](#fn.2)</sup>
*   不同的项目有不同的窗口配置和进程，只需按几个键就可以调用(提示:键盘宏)
*   一个集成的 git 瓷器，实际上可以帮助您更快地学习 git(magit)
*   将电子邮件与任务、项目、文档和工作流紧密集成(mu4e，Org-mode)
*   在您的终端中提供定制的文本编辑器(使用从终端中的守护程序启动的电子邮件客户端)
*   不需要使用鼠标导航！ <sup>[3](#fn.3)</sup>

现在:想象一下在一个合理的简化状态下，拥有以上所有东西的综合效果。然后，考虑多个项目的累积效应！以上只是对 Emacs 的可能性的一个浅显的概述。

从长远来看，投资学习 Emacs 具有产生指数级结果的巨大潜力。

## 脚注

<sup>[1](#fnr.1)</sup> 关于使用 Yasnippet 的文章:——[使用 Emacs yasnipet 对抗重复的样板代码](http://blog.refu.co/?p=1355) || [调整 Emacs yasnipet](https://jpace.wordpress.com/2012/10/20/tweaking-emacs-snippets/)| |[扩展代码片段](https://joaotavora.github.io/yasnippet/snippet-expansion.html)

<sup>[2](#fnr.2)</sup> 链接将 R 与 Emacs 一起使用:[将 R 与 Emacs 和 ESS 一起使用](https://www.r-bloggers.com/using-r-with-emacs-and-ess/) || [将 R 与 Emacs 一起使用](https://lucidmanager.org/using-r-with-emacs/) || [来自使用 ESS 的 R 程序员的提示](https://www.reddit.com/r/emacs/comments/8gr6jt/looking_for_tips_from_r_coders_who_use_ess/) || [我为什么要将 Emacs 用于 R 编程](https://thescientificshrimper.wordpress.com/2018/12/12/soapbox-rant-why-i-use-emacs-for-r-programming/)

<sup>[3](#fnr.3)</sup> 参见这篇[非技术用户实验](http://rss.slashdot.org/~r/Slashdot/slashdot/~3/7iykh9HdS5U/i-stopped-using-a-computer-mouse-for-a-week-and-it-was-amazing)的文章，报告称速度和生产力显著提高。我自己在获得基本熟练的移动 Emacs 后也经历过这种情况。