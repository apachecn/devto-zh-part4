# John Deere 割草机 Plus 数据库探索总结

> 原文：<https://dev.to/hrbrmstr/wrapping-up-exploration-of-john-deere-s-mowerplus-database-4cec>

我在第二次割草后浏览表格时，在[a forebolded MowerPlus 数据库](https://dev.to/hrbrmstr/trawling-through-ios-backups-for-treasure-a-k-a-how-to-fish-for-target-files-in-ios-backups-with-r-3n15)上做了另一个 twitter 线程，以确定什么确定了一次独特的割草“会话”(使用 John Deere 的术语)。

这是线索:

> 如前所述，今天是第二次使用新的 [@JohnDeere](http://twitter.com/JohnDeere) 割草机。我将线程的探索什么是数据库看起来像 2 mows 记录+有一个新的 macOS 10.15 iOS 备份窗口的屏幕截图。(也将线程化)
> 
> —鲍勃 [![🇷](img/85cb8c7942e552838bef70099cad338a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uzZTarNF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f1f7.png) 乌迪斯([@ hrbrmstr](http://twitter.com/hrbrmstr))[2019 年 6 月 9 日](https://twitter.com/hrbrmstr/status/1137747919166218242?ref_src=twsrc%5Etfw)

对于那些明显反对 Twitter 但一直在博客中关注的人来说，总体 TLDR 是，这一次花费的时间更少，因为我不必查看两次区域，并且`ZMOWLOCATION`表中的`ZSESSION`列保存了给定割草会话的会话 id。

我把如何访问保存这些数据的 MowerPlus SQLite 数据库打包到一个函数中，你可以在这里看到一个非 Twitter(和无注释)版本的 Twitter 线程——[这个 rmarkdown 报告](https://rud.is/rpubs/mow-explore.html)——或者下面:

### 鳍

我可能会为此制作一个小的包，因为我将在割草季节使用它，所以如果您也想使用 R 来帮助分析您的割草机数据，请检查常见的可疑程序(sourcehut/gitlab/gitugh)。