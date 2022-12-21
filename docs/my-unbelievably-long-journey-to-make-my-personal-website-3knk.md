# 我难以置信的漫长旅程来制作我的个人网站

> 原文：<https://dev.to/gablaroche/my-unbelievably-long-journey-to-make-my-personal-website-3knk>

就像我的网站一样，我不久前开始了这篇文章，但是我想在我发布我的网站后完成它，所以这可能会以大量的散漫和完全没有结构而告终。

几年前，我开始建立自己的网站。我写了一些代码，然后无数次把所有东西都删了，我还有几个。私营部门司和。我的电脑里放着我设计了一半的网站。我知道我不会尊重我做的任何设计，所以我决定顺其自然。

我的作品集的这个版本，在我把代码推向 prod 之前花了我大约 3 个月的时间，我只在我喜欢的时候为这个项目写代码，所以不经常。因为这个项目我主要使用了两台不同的电脑，所以我决定把我的项目放在 github 上，同时我把代码开源了。

我从未完全完成 mt 网站的一个主要原因是因为我缺乏个人项目来展示，我只有我的 [IE11 死亡倒计时](https://gabriellaroche.dev/ie11-death-countdown/)。我想在发布我的网站之前有更多的项目。有趣的是，我甚至没有在找工作，我只是想要一个网站，因为我想任何网络开发人员都应该有一个网站。

我用 PHP，SASS 和 TypeScript 写了网站。当我第一次开始这个项目时，我想只使用 HTML5 纯 CSS3 和 vanilla JS，但我希望我的网站是双语的(法语和英语)，所以我想到使用 JSON 文件和处理 javascript 的翻译，并使用 web 组件来构建不同的部分，但后来我意识到这可能不是性能的最佳选择，如果 javascript 被禁用，所以我决定使用 PHP 和 JSON 文件的混合来处理翻译(帖子来解释这个选择)。在选择 JSON 存储翻译之前，我想像在学校一样使用 [gettext](https://www.gnu.org/software/gettext/) 和 [Poedit](https://poedit.net/) ，但是这些知识太遥远了，而且所有在线教程都不清楚。因此，我决定使用 JSON 文件和 PHP 来使用一种不太容易维护的方法。

我为这篇漫无边际的帖子道歉，但我必须谈谈我制作个人网站的旅程，你可以在这里查看: [gabriellaroche.dev](https://gabriellaroche.dev) 和回购权[这里](https://github.com/gabLaroche/portfolio)。这真的感觉像是卸下了我肩上的重担，它远非完美，但我对最终的结果很满意，我很高兴我终于完成了这个项目。