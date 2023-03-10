# 我如何差点造出一个推特机器人

> 原文：<https://dev.to/renegadecoder94/how-i-almost-built-a-twitter-bot-77g>

最近，我一直在寻找方法来增加我在社交媒体上的关注，这样我就可以更直接地向社区推销我的内容。为了做到这一点，我几乎做了一个推特机器人。

## 背景

作为一点背景，我一直在努力建立一个社区有一段时间了。除了我的妻子，我真的没有任何人帮助我推动叛徒编码器，自然这导致了一些非常缓慢的增长以外的搜索引擎流量。

随着时间的推移，我已经慢慢扩大了我的邮件列表，目前大概有 50 个邮件地址。[自从搬到帕特里翁](https://www.patreon.com/TheRenegadeCoder)，我真的无法维持任何形式的会员资格。最重要的是，我也很难维持对我的开源项目的支持。

那么，怎么回事？我该如何发展我的追随者？毕竟我一直以为:你建了，他们就来了。现在，我已经写了超过 250 篇文章，所以有足够多的文章供人们阅读。当然，我得到了大量的有机流量。问题是我没有保持任何形式的关注。

显然，这种挣扎对我的一些 Twitter 粉丝来说变得越来越明显，其中一人甚至伸出手来给我一些建议。特别是，他们与我分享了下面的视频，希望能帮助我增加我的粉丝:

[https://www.youtube.com/embed/7ovFudqFB0Q](https://www.youtube.com/embed/7ovFudqFB0Q)

突然，我开始写一个推特机器人。

## 视频摘要

如果你没有时间看完整个视频，这里有一个基本的想法:喜欢推文会给你带来追随者，那么为什么不自动化喜欢的过程呢？

说实话，我没有看完整个视频，但我明白了要点。基本上，YouTuber 编写了一些 Python 脚本来扮演一个人点击 Twitter 并喜欢 Twitter。

我的理解是，你必须在浏览器中打开一页推文，然后让机器人接管。换句话说，它将遍历 DOM 并对视图中的所有 tweet 执行喜欢操作。然后，它会刷新页面并重复这个过程。

对我来说，这似乎有点过头了。我对 Twitter 和它的 API 知之甚少，但我只知道一定有更好的方法。于是，我不再看视频，试着自己做。

## Tweepy 和 Twitter API

事实证明，Twitter 有一个 API，可以用来执行所有这些动作，而不必编写一些复杂的程序来模拟一个人。相反，API 允许您使用一些凭证登录，并执行搜索查询来检索一组可以交互的 tweets。

更简单的是，有一个包装 Twitter API 的 Python 库，名为 Tweepy。有了这个库，你就可以登录 Twitter，用一些关键词搜索推文，用不到十行代码就可以喜欢上所有这些推文。

然而，要做到这一点，您需要通过 Twitter API 生成特殊的凭证。为此，您必须创建一个开发者帐户，将您与这些凭据绑定在一起。

在经历了创建开发者账户的过程和几乎创建第一套证书的过程后，我偶然发现了推特自动化规则的链接。这些规则明确规定了在 Twitter API 的自动化方面可以做什么和不可以做什么。

如果你向下滚动到该页面的底部，你会看到一个标题为“d .你对推文或账户采取的自动操作”的部分在这一部分，你会发现以下规则:

> 自动喜欢:你可能不喜欢自动喜欢推文。

换句话说，使用机器人来喜欢推文违反了 Twitter 的政策。

## 故事的寓意

尽管在创建开发者账户的过程中发现我无法创建我的机器人令人沮丧，但我不得不为这样的规则的存在感到高兴。毕竟，看起来 Twitter 实际上是在努力让他们的社区变得更好。

也就是说，我不喜欢浪费时间，所以我想我应该写这篇文章。至少那样的话，不会让人觉得一切都是徒劳。

此外，我想首先为这个想法喊出 Jose Fon。我真的很感谢这种社区支持！

一如既往，如果你喜欢这篇简短的文章，如果你能分享一下，我将不胜感激。此外，如果你准备好了，我希望你[加入我的 Patreon](https://www.patreon.com/TheRenegadeCoder) 或者至少[加入邮件列表](https://newsletter.therenegadecoder.com/)。

当你在这里的时候，看看我最喜欢的一些文章:

*   [使用模运算的石头剪刀布](https://dev.to/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)
*   [语句和表达式的区别](https://dev.to/renegadecoder94/the-difference-between-statements-and-expressions-1e47)
*   [如何在 Python 中检查文件是否存在](https://therenegadecoder.com/code/how-to-check-if-a-file-exists-in-python/)

再次感谢你的支持。点点滴滴，积少成多！

帖子[我如何差点造出一个推特机器人](https://therenegadecoder.com/blog/how-i-almost-built-a-twitter-bot/)首先出现在[这个变节的程序员](https://therenegadecoder.com)上。