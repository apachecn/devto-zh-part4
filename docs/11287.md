# 制作龙珠童子军字体

> 原文：<https://dev.to/haideralipunjabi/making-dragon-ball-scouter-font-ebh>

### 背景

我从小就是一个超级龙珠迷。2015 年龙珠超获释后，我加入了[r/dbz](https://www.reddit.com/r/dbz/)subredit，与 dbz 的一切保持联系。

龙珠超:系列中的最新电影《兄弟》扩展了赛亚人的历史，因此，这是龙珠历史上第一次看到如此高质量的童子军。为那些致力于使童子军语言变得可理解而不是一些随机的胡言乱语的人致敬。

2019 年 4 月，一位 Redditor [u/SummerFlux](https://www.reddit.com/user/SummerFlux/) 破译了整件事，并在[这个](https://www.reddit.com/r/dbz/comments/beh38x/i_figured_out_what_the_scouters_say_dbs_broly)和[这个](https://www.reddit.com/r/dbz/comments/bf6ndk/the_completed_scouter_alphabet_follow_up/)帖子中发布了关于这件事的消息。我一直想学习如何制作字体，这似乎是一个很好的机会。

### 第一天(2019 年 4 月 19 日)

第[条帖子](https://www.reddit.com/r/dbz/comments/beh38x/i_figured_out_what_the_scouters_say_dbs_broly/)于 2019 年 4 月 18 日上午 9:30 左右(IST)发出。在我注意到它之后(已经过了 10-12 个小时)，我花了几个小时准备和学习字体制作。我决定使用 [Inkscape](https://inkscape.org/) 制作字形，然后使用 [FontForge](https://fontforge.github.io/) 制作字体文件。在我的各种想法中，有两个似乎是可行的。

1.  从 DBS: Broly 中使用的文本图像中追踪字体
2.  在一个 3x3 的网格上画出字形，就像在纸上画的那样

我决定两样都做，从追踪开始。首先我下载了这篇文章中 [u/SummerFlux](https://www.reddit.com/user/SummerFlux/) 使用过的所有图片。然后，我在 Inkscape 中一个接一个地打开它们，裁剪了文本部分，并在上面使用了跟踪位图功能。*(注意:大多数矢量图像编辑器(Illustrator、Corel Draw 等)都有类似于跟踪位图的功能。您需要尝试不同的设置，以获得最佳效果)*。我为每个字形制作了单独的 SVG 文件(也包括未知的)。

通过使用 FontForge，生成字体文件变得很容易。我看过其他的字体库来找出我需要生成的文件。我生成了 True Type 字体(。ttf)、OpenType 字体(。otf)、SVG 字体(。svg) & Web 开放字体格式(。woff)。

到那天结束时，我已经有了一个包含 31 个字形(20 个字母，10 个数字和 1 个符号)的字体文件。).我希望有人能够破译至少剩下的 6 个字母，但这似乎很难，因为它们从未在整部电影中使用过。

### 第二天(2019 年 4 月 20 日)

两天后，当我打开 Reddit 和 Github 查看进度时。有人在 [u/SummerFlux](https://www.reddit.com/user/SummerFlux/) 发表的关于完整破译字母表的[后续帖子中提到了我。我很好奇他/她是如何做到这么快。他/她在他/她已经破译的字母中发现了一种模式，其中一些字形在每 7 个字母后以改变的方向重复。根据这个模式，他/她想出了整个字母表。](https://www.reddit.com/r/dbz/comments/bf6ndk/the_completed_scouter_alphabet_follow_up/) [*破译图案的图像*](https://i.redd.it/cdz6qmpafbt21.jpg)

我要去上大学，所以我决定带着我的笔记本电脑，提前完成字体。我花了大约 10-15 分钟完成，并在大学开学前提交了对 GitHub 的修改。

### 3x 3 网格字体设计:

在完成了基于字体的描摹之后，我开始进行 3x3 的基于网格的设计。我花了大约 30 分钟才完成，当我正要上传它们时，我注意到 [u/TopHoJo](https://www.reddit.com/user/TopHoJo) 对他/她制作的[字体的评论](https://www.dropbox.com/s/428kdonzcxgra6a/SaiyanScouter-Regular%284%29.ttf?dl=0)。它也是基于一个 3x3 的网格，看起来和我的作品相似(不完全相同)。我喜欢这个字体，决定不上传我的字体，优先选择现有的字体。

### 新闻中:

u/SummerFlux 的作品被[在线新闻网站《新闻周刊](https://www.newsweek.com/dragon-ball-super-brolys-mysterious-scouter-language-deciphered-redditor-1401012)特别报道。我相信它没有得到应有的关注，希望将来有人会再次注意到它。

### 参考文献:

*   [u/SummerFlux](https://www.reddit.com/user/SummerFlux/) 的[岗位 1](https://www.reddit.com/r/dbz/comments/beh38x/i_figured_out_what_the_scouters_say_dbs_broly) & [岗位 2](https://www.reddit.com/r/dbz/comments/bf6ndk/the_completed_scouter_alphabet_follow_up/)
*   [我的字体 Github 库](https://github.com/HackeSta/DBSScouterFont)
*   [SaiyanScouter-Regular](https://www.dropbox.com/s/428kdonzcxgra6a/SaiyanScouter-Regular%284%29.ttf?dl=0) -另一种 Scouter 字体，由 [u/TopHoJo](https://www.reddit.com/user/TopHoJo) 在 3x3 的网格上从头开始制作
*   [墨水匣](https://inkscape.org/)
*   [FontForge](https://fontforge.github.io/)
*   [新闻周刊文章](https://www.newsweek.com/dragon-ball-super-brolys-mysterious-scouter-language-deciphered-redditor-1401012)
*   [描摹位图教程](https://www.youtube.com/watch?v=i-xxhphybnE)