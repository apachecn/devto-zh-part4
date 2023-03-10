# 现在我读什么？一个快速推荐书籍的网络应用。

> 原文：<https://dev.to/schollz/now-what-do-i-read-a-web-app-for-blazing-fast-book-recommendations-1jhd>

<small>*[(来自 nowwhatdoiread.com 的 x-post)](https://nowwhatdoiread.com/blog)*</small>

[![Cat on books](img/56b4511aa0d5f3119fde714f594c0e79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fLaseduM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nowwhatdoiread.com/static/catonbooks.png)

你现在正在看什么书？你喜欢 T2 吗？

如果你对这些问题中的任何一个回答“是”*，那么停止阅读这篇博客，去读你的书吧！*

 *如果你对这些问题中的任何一个回答“*否*”，那么就是*时间*。是时候找一本新书来读了。

“但是，”你声称，“没有什么可读的。”我相信你。我相信，因为你问过你的朋友，你搜索过 Amazon.com，你查过 Goodreads.com，你浏览过图书馆的书架，巴诺书店的书架，没有一本书引起你的注意。现在，你已经正确地得出结论，没有什么可以读的。

暂时不要放弃寻找。有没有*有没有*一本书是专门为你准备的——这本书是如此的*好*以至于即使到了晚上 11:30，你还得在早上 5 点起床去工作，你都不会放下它。

我怎么知道有一本书适合你？我知道是因为我也经历过同样的情况。我寻找过，厌倦了寻找，放弃了。但后来我找到了更好的搜索方式。这个更好的搜索让我找到了一本书，是专门为我写的。这种找书的方式已经不止一次或两次奏效了，但每次都是这样。现在，每当我有问题的时候，我都能找到一本很棒的书:**“现在，我在读什么？”**

## 搜索书籍的新方法

我如何找到我要读的下一本书？我在 2019 年做这件事的方式和每个人一样:使用一个*网络应用*。

网络应用程序只是一个可以在互联网上访问的计算机程序的别称。一个网络应用程序的计算机程序可以在其电路大脑中保存数百万本书的信息。它可以使用千兆赫处理器找到最相关的书籍建议，并使用互联网将其显示在网站上。与你的朋友和友好的图书管理员不同，网络应用可以在任何时候接受任何请求，并在几毫秒内给你一个不带偏见的书籍建议。

我用电脑做了这样一个 web app。而且，由于我的电脑不属于亚马逊/巴尼百货和诺布尔/鲍威尔斯或其他在线书商，它不会试图操纵你，因为它想让你买东西。 <sup class="footnote-ref" id="fnref:2">[1](#fn:2)</sup>

注意:我的网络应用程序*使用的是亚马逊联盟链接，它会为我的链接点击付费。这笔钱用于支付托管费用(12 美元/年)，之后我通过[亚马逊保护协会](https://www.amazonconservation.org/)将所有利润用于支持真正的亚马逊。*

我的网络应用程序进一步优化，只显示具有相似特征的最佳书籍，并排除同一作者的续集或多本书。 <sup class="footnote-ref" id="fnref:1">[2](#fn:1)</sup>

用 Golang 写的 web app。其速度的关键是利用[獾密钥库](https://github.com/dgraph-io/badger)并预先计算一百万本书的所有相似之处。使用速度惊人的 [junegunn/fzf](https://github.com/junegunn/fzf) 库提供模糊搜索。图像通过 base64 进行内联，使所有资产捆绑在一起，并通过 [tdewolff/minify](https://github.com/tdewolff/minify) 进行缩小，以获得额外的速度提升。此外，没有 Javascript，所以服务器做了繁重的工作，使渲染看起来是即时的。

这个网络应用叫做 [`nowwhatdoiread.com`](https://nowwhatdoiread.com) 。来吧，试试看。

## 使用*的例子*

 *这是我生活中的一个例子。出于某种原因，我读了罗伯特·麦卡蒙的《男孩的生活》。我以为这是一部传记，但结果却是魔幻和恐怖的。我通常不读恐怖小说，但我喜欢这本书。我以为这样的书不会很多。

我用 [`nowwhatdoiread.com`](https://nowwhatdoiread.com) ，输入“*少年生活*，找到了可利夫·巴克的 [*织世界*。这本书让我着迷，我喜欢它胜过《男孩的生活》。但是，在看完《编织世界》之后，我又沮丧了。它是如此的好和独特，我想我不会再找到任何接近它的东西了。但后来，我在网络应用上找了类似于 *Weaveworld* 的书，找到了斯科特·霍金斯](https://nowwhatdoiread.com/book/471747102-weaveworld-by-clive-barker?q=weaveworld)的 [*查尔山*图书馆。这本书甚至更好！](https://nowwhatdoiread.com/book/2364775265-the-library-at-mount-char-by-scott-hawkins)

我仍然发现使用 [`nowwhatdoiread.com`](https://nowwhatdoiread.com) 的伟大和相关的书籍建议。我放在查尔山图书馆*的最后一本书，现在我正在读中国城市的[*北海巨妖，我很喜欢。*](https://nowwhatdoiread.com/book/3143870535-kraken-by-china-mieville)*

 *这个例子让我找到了一套特定的类型，但我发现类型并不重要，我得到了所有其他类型书籍的良好推荐。我找到了所有类型的书籍建议，因为 web 应用程序对特定类型没有任何偏见。

## 这个循环还在继续

我现在读的每一本书都在这个网络应用 [`nowwhatdoiread.com`](https://nowwhatdoiread.com) 的帮助下让我有更多的书可以读。虽然我主要是为自己制作的，但我现在向全世界开放，这样你就可以找到一本书，同时支持一个慈善机构。

也许对你有用。也许不会。

无论如何，我希望不要问“现在，我读什么？”你可以直接说*“现在，我读书了。”*

[![](img/7052e983eca269f68da965ef59b963e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ELZRROlk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nowwhatdoiread.com/static/cat.png)***