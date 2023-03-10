# 快速命中:一些 ggplot2 统计💙对于{logspline}

> 原文：<https://dev.to/hrbrmstr/quick-hit-some-ggplot2-stat-for-logspline-296f>

在过去的大约 6 个月里，我已经成为了 [{logspline}软件包](https://cran.rstudio.org/web/packages/logspline/index.html)的忠实粉丝，并决定将一个手动的 ggplot2 绘图过程(嗯，至少是在一个 RStudio 片段中)打包成一个小的{ggplot2} `Stat`，以便更容易地可视化拟合模型的各种组件。

如果你是对数样条密度估计的新手，这篇由 Stone、Hansen、Kooperberg 和 Truong 撰写的文章是对{logspline}包中使用的方法和(主要)算法的一个非常容易理解的介绍。

如果您已经是{logspline}软件包的用户，或者一直在使用该软件包的基本 S3 绘图功能，但希望使用{ggplot2}中的功能，这是为您设计的。

如果你点击 https://git.rud.is/hrbrmstr/gglogspline[网站](https://git.rud.is/hrbrmstr/gglogspline)，那里有你最喜欢的社交代码协作服务的链接以及安装说明。有一个单一的`Stat`—`stat_logspline()`——做所有的艰苦工作。默认情况下，它将使`y`成为`stat(density)`计算字段，但是您也可以使用其他计算字段:

*   `count`:计算出的计数估计值(类似于`ggplot2::stat_density()`)
*   `probs`:分配功能
*   `survival`:生存功能
*   `hazard`:危险功能

可以看看下面`geom_histogram()`、`geom_density()`、`ggalt::geom_bkde()`、`gglogspline::stat_logspline()`的(轻)对比(或者用[这个链接](https://rud.is/rpubs/gglogspline-example.html)来爆 iframe):

### 鳍

对于“通用”密度估计，您可能仍然依赖于`stat_density()`,但是在您深入了解 logspline 的背景并在一些适当的用例中试用它之后，您可能会像我一样喜欢它。

包装本身超小，重点突出。其中一个原因是它可能对那些想创建或定制自己的 ggplot2 `Stat`的人有所帮助(我想我在这篇文章之前忘记把许可证改成 MIT 了，所以我会尽快改正；我默认了 AGPL，这是一个相当可悲的尝试，试图阻止令人讨厌的网络安全初创公司——几乎 99%的公司——窃取代码而不回馈社区。

像往常一样，当你感到感动时，踢踢轮胎，提出问题或 PRs(以及任何你觉得舒服的社交编码)。