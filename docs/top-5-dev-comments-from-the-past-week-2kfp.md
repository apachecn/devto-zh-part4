# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-2kfp>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

[@kungtotte](https://dev.to/kungtotte) 对 **[Github 必须是免费平台](https://dev.to/nekofar/github-must-be-a-free-platform-14pj)** 线程补充了一些想法。整个线程和周围的环境值得阅读更多:

[![kungtotte profile image](img/2f7bfd2e72fd231563e9df61fd95d8e4.png) ](/kungtotte) [ Thomas Landin ](/kungtotte) • [<time datetime="2019-07-27T04:21:43Z" class="date-short-year"> Jul 27 '19 </time>](https://dev.to/kungtotte/comment/de60) 

问题不在于 GitHub 不是免费的，问题在于出于某种原因，开源社区集体决定所有的开发都必须在 GitHub 上进行:一个专有的平台。

GitHub 有几个替代方案，您可以不受美国法律的约束自行托管(托管在不制裁伊朗的国家之外)，尽管对于一个单独的开发人员来说独自完成可能太难了，但开源社区应该联合起来，资助在不受这些制裁的地方建立 GitLab/Gogs/Gitea/Sourcehut 实例。

这个问题会发生在美国以外的任何服务上，这种情况下违反了法律。不是 GitHub。

在一个注定会有一些热点和分歧的帖子中: **[最好的 JavaScript 框架是什么？](https://dev.to/ben/what-s-the-best-javascript-framework-3ple)** ， [@dmtrkovalenko](https://dev.to/dmtrkovalenko) 提供了一些爆笑的误导和轻浮:

[![dmtrkovalenko profile image](img/cb34d8f3259b64e75168e278fb93c7a4.png) ](/dmtrkovalenko) [ Dmitriy Kovalenko ](/dmtrkovalenko) • [<time datetime="2019-07-28T15:30:01Z" class="date-short-year"> Jul 28 '19 </time> • Edited on <time datetime="2019-07-28T15:32:05Z" class="hidden m:inline-block date-no-year">Jul 28</time>](https://dev.to/dmtrkovalenko/comment/dfak) 

[![meme](img/e4c0dcf461f70b6895e7a9d476ba7563.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--arIqg8TV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/of1w1n3a21bmplo68arm.jpeg)

一个“改变我的想法”的线程经常会走上非建设性的道路，所以很高兴看到 [@stereobooster](https://dev.to/stereobooster) 的评论和随后的独立文章回应 **[TypeScript 是浪费时间。改变我的想法。](https://dev.to/parkroolucas/typescript-is-a-waste-of-time-change-my-mind-pi8)** :

[![stereobooster profile image](img/85dd06cbd01e5925246fdc462edccfe5.png) ](/stereobooster) [ stereobooster ](/stereobooster) • [<time datetime="2019-07-28T07:31:23Z" class="date-short-year"> Jul 28 '19 </time> • Edited on <time datetime="2019-07-30T05:57:19Z" class="hidden m:inline-block date-no-year">Jul 30</time>](https://dev.to/stereobooster/comment/df11) 

**dev.to/stereobooster/type-system-f...**:我把这个帖子变成了文章

好了，让我们通过修正一些术语来开始建设性的对话:JS 是类型化语言，它是动态类型检查语言(或者有些人将其简称为动态类型)。JS 有多种类型:字符串、数字、布尔值等。JS 有类型错误(不多，但还是有)

```
null.func // Uncaught TypeError: Cannot read property 'func' of null at <anonymous>:1:6 
```

Enter fullscreen mode Exit fullscreen modeEnter fullscreen mode Exit fullscreen mode
Enter fullscreen mode Exit fullscreen mode

JS 具有隐式强制，这可能会造成 JS 中没有类型的印象。点击阅读更多[。](https://dev.to/stereobooster/pragmatic-types-is-javascript-an-untyped-language-1k03)

所以使用还是不使用 TS 的问题归结为:到底要不要做静态类型检查？(动态类型检查总是存在)。这是一种交易:为了让静态类型检查器工作，您需要多写一点代码(类型注释)。是否值得由你来决定。

同样，您可能会对类型系统的可靠性感到困惑。一些类型系统是可靠的，例如，它们可以证明没有类型错误。TS 不是 sound ( `any` type)，但是您可以通过将所有严格检查设置为 true，用 tsconfig 保护它。并且通过禁止使用显式的`any`。

> 但是，因为 TypeScript 向下编译成 JavaScript，所以不管您的类型设计得多么仔细，总有可能不同的值类型混入 JavaScript 变量中。

你可以在静态类型系统和动态类型系统之间架起一座桥梁。检查输入类型是否动态有效(未知且不受控制),并通过程序员的其余部分依赖静态类型检查器

例如:

```
const makeSureItIsAString = (x: any) => {
    if (typeof x !== "string") throw new TypeError("Not a string")
    return x;
}
const nickname = makeSureItIsAString(userInput())
// we are sure that nickname is a string in this part of the code 
```

Enter fullscreen mode Exit fullscreen modeEnter fullscreen mode Exit fullscreen mode
Enter fullscreen mode Exit fullscreen mode

点击阅读[更多关于 io 验证的信息](https://dev.to/stereobooster/pragmatic-types-io-validation-or-how-to-handle-json-based-apis-in-statically-typed-language-1d9m)

```
</div> 
```

Enter fullscreen mode Exit fullscreen mode

回复 **[你的电脑是什么规格？哪些规格对你来说特别重要？](https://dev.to/ben/what-are-your-computer-s-specs-which-specs-are-particularly-important-to-you-4a0c)** ， [@steveblue](https://dev.to/steveblue) 谈到他们的调教选择:

[![steveblue profile image](img/324f14b1391f0a0dfc72b3f658529b42.png) ](/steveblue) [ Stephen Belovarich ](/steveblue) • [<time datetime="2019-07-27T23:55:55Z" class="date-short-year"> Jul 27 '19 </time> • Edited on <time datetime="2019-07-28T01:03:09Z" class="hidden m:inline-block date-no-year">Jul 28</time>](https://dev.to/steveblue/comment/dema) 

[![](img/692afbd8e15bc5605ebc62e2391ac14b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gcrpYu6Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3c93v5ji6mbzunl0coql.jpg)

2.3 GHz 英特尔酷睿 i9 w/睿频加速高达 4.8 GHz
32gb 2400 MHz DDR 4✅
radeon pro 560 x 4gb
sonnet egpu w/8gb 蓝宝石脉冲 Radeon RX 580 ✅
500GB 苹果固态硬盘✅

这次我选择了第二强的苹果 MBP。最强大的配置是超频，不想搞砸了。

有人告诉我，我不需要 32GB 内存，但到目前为止，它还没有伤害！

SSD 必不可少。我用了一段时间没有固态硬盘的旧电脑，我在开发的时候感觉到了。

我怎么推荐 eGPU 都不为过。它驱动我的显示器，所以我的 MBP 不需要(我只需要插入一根电缆来连接我的 MBP)。最重要的是，我发现多个应用程序实际上会利用它，包括网络浏览器。当我尝试 WebGL 时，这是一个巨大的优势。每当我想升级时，我也可以换出显卡。

对于在 **[中分享了一些不可思议的胜利的](https://dev.to/jess/what-was-your-win-this-week-1a02) [@desi](https://dev.to/desi) 来说，这是一次非常棒的比赛。你本周的胜利是什么？线程 T5👏:** 

[![desi profile image](img/2dd2e4c6084f58c555583821599e34f7.png) ](/desi) [ Desi ](/desi) • [<time datetime="2019-07-26T18:46:31Z" class="date-short-year"> Jul 26 '19 </time>](https://dev.to/desi/comment/ddml) 

*   和我的搭档跑了 10 公里！
*   庆祝你的《代码》第一年出版，其中有一章是我写的！
*   哦，订婚了，☺️☺️☺️💍💍💍

下周见，更多精彩评论，✌