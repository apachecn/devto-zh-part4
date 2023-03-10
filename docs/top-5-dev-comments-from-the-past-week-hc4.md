# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-hc4>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

我是这些发生在 DEV 上的 [#meta](https://dev.to/t/meta) 对话的超级粉丝，而不是在 GitHub 问题上孤立的。在 **[中，您认为 DEV 上的“阅读位置”指示器是什么？](https://dev.to/devteam/what-would-your-opinion-be-of-a-reading-position-indicator-on-dev-1088)** [@cjbrooks12](https://dev.to/cjbrooks12) 献上自己的想法:

[![cjbrooks12 profile image](img/97fbb987cec86db9d610eae97c0dfa7b.png) ](/cjbrooks12) [ Casey Brooks ](/cjbrooks12) • [<time datetime="2019-08-16T16:02:52Z" class="date-short-year"> Aug 16 '19 </time>](https://dev.to/cjbrooks12/comment/e7j3) 

我通常认为它们往好里说是不必要的，往坏里说是分散注意力。把“阅读时间”列在文章的顶部，足以让我知道这篇文章有多长。但是有些人真的很喜欢拥有它们，所以也许可以为用户提供一个设置，根据他们的个人喜好来打开/关闭它？

我认为我不喜欢通用进度条的原因是，它不能真正给我关于阅读进度的有意义的信息。我可能读到了文章的一半，但是剩下的文章有多少是代码片段、交互式例子、视频、散文、结论等等？我阅读这篇文章的实际进度可能会被非原始文本的东西严重扭曲，所以总的来说，感觉很随意。

一个更好的进度衡量方法可能是列举文章内容中的标题，并突出显示我所通过的标题的进度。一个很好的例子就是 [Android docs](https://developer.android.com/topic/libraries/architecture/livedata) 的右侧菜单。看到我已经有了两个内容部分、一个例子和一个结论，直到我写完这篇文章，这比任意衡量文本更有意义*。此外，它允许我直接跳到页面上的特定部分。*

回复 **[你可能不需要前端框架](https://dev.to/steelvoltage/you-probably-don-t-need-a-front-end-framework-26o6)** ，@mattwaler 提供了一些微妙的观点，这些观点在评论中引发了更有成效的对话:

未找到注释

回答 **[你工作中最难的部分是什么？](https://dev.to/molly_struve/what-is-the-hardest-part-of-your-job-21il)** ，@molly_struve 谈领导团队的挑战和重要性:

[![molly profile image](img/5c34463ea6ee7c5f65b3228848d6db47.png) ](/molly) [ Molly Struve (she/her) ](/molly) • [<time datetime="2019-08-15T20:47:14Z" class="date-short-year"> Aug 15 '19 </time>](https://dev.to/molly/comment/e6o9) 

我最近成为了一名技术主管，现在我负责一个 2 人团队。起初，我认为我加上另外两个人意味着我们将完成比以前多三倍的工作，这是小菜一碟。哦，天哪，我错了！领导一个团队是一项艰苦的工作！弄清楚每个成员需要多少支持以及如何最好地给予他们支持，同时继续在技术上贡献自己是很困难的。我不得不接受贡献少没关系，因为我的时间更好地用于分享我的知识和帮助团队中的其他人成长。

TL；博士:我工作中最难的部分是学习如何有效地领导团队。

我相信这是第一个来自 [#challenge](https://dev.to/t/challenge) 的评论成为前 5 名评论，但它值得等待。在 **[每日挑战#43 -桌游战斗解决者](https://dev.to/devteam/daily-challenge-43-boardgame-fight-resolver-53e3)**[@ alvaromantoro](https://dev.to/alvaromontoro)提供了一个基于 CSS 的解决方案，让一些人目瞪口呆:

[![alvaromontoro profile image](img/78bd08d78aaa0cbf6225b24b72a08ffd.png) ](/alvaromontoro) [ Alvaro Montoro ](/alvaromontoro) • [<time datetime="2019-08-17T17:03:49Z" class="date-short-year"> Aug 17 '19 </time> • Edited on <time datetime="2019-08-17T17:09:03Z" class="hidden m:inline-block date-no-year">Aug 17</time>](https://dev.to/alvaromontoro/comment/e8km) 

半铸钢ˌ钢性铸铁(Cast Semi-Steel)

将属性`data-piece1`和`data-piece2`添加到任意元素，它将宣布获胜者:

```
[data-piece1][data-piece2]::before {
  content: attr(data-piece1) ' vs ' attr(data-piece2) ': ';
}

/* By default, piece 1 will win */
[data-piece1][data-piece2]::after {
  content: attr(data-piece1) ' wins!';
}

/* Only in some particular cases, piece 2 will win */
[data-piece2="archers"i][data-piece1="swordsmen"i]::after,
[data-piece2="swordsmen"i][data-piece1="pikemen"i]::after,
[data-piece2="pikemen"i][data-piece1="cavalry"i]::after,
[data-piece2="cavalry"i][data-piece1="archers"i]::after { 
  content: attr(data-piece2) ' wins!'
} 
```

Enter fullscreen mode Exit fullscreen mode

在 [CSS 选择器 Level 4 中，有一个选项可以让一些选择器不区分大小写](https://drafts.csswg.org/selectors-4/#attribute-case)(如上图所示在末尾加一个`i`)。而且支持的还算不错(没在 IE 上试过)。所以不管用户写的是“弓箭手”还是“弓箭手”还是“弓箭手”，都会匹配出来。

[https://codepen.io/alvaromontoro/embed/gOYMGJL?height=600&default-tab=result&embed-version=2](https://codepen.io/alvaromontoro/embed/gOYMGJL?height=600&default-tab=result&embed-version=2)

最后，进入 **[【问 Dev】懈怠对工作有干扰吗？](https://dev.to/jacobherrington/ask-dev-is-slack-disruptive-at-work-4da8)** thread，@neilonsoftware talks disruption，synchronous v. async，以及他们保持专注和建立团队沟通渠道的战术:

未找到注释

下周见，更多精彩评论，✌