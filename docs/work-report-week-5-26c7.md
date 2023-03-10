# 工作汇报！第五周

> 原文：<https://dev.to/antoniogamiz/work-report-week-5-26c7>

我又来了！这个星期我有点忙于个人事务，所以没能像预期的那样努力工作。不管怎样，已经取得了不少进展。

# 分离舱::便利[问题#2696](https://github.com/perl6/doc/issues/2696)

这个模块在 [perl6/doc](https://github.com/perl6/doc) 中的很多不同的文件中使用。它的主要目的是提供处理 Pod 对象的功能:创建、修改它们，等等。我已经在 [pod::Utilities](https://github.com/antoniogamiz/Pod-Utilities) 中将其分离出来，其中代码被分成修改 Pod 结构的函数和创建 Pod 对象的函数。

# Pod::To::cache

在一些问题中可以看到( [#1952](https://github.com/perl6/doc/issues/1952) ， [#717](https://github.com/perl6/doc/issues/717) ，...)，我们确实需要一个缓存系统来减少测试和构建时间。解析所有 pods 以获得 Perl6 代码需要一段时间，目前在这个过程中大约要做三次。

我们想要的缓存系统是这样的:

[![](img/7f5b66d53abe703bb9517524a8189224.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_CUbJYo3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s2v4lc1s7awwlhu7ryxr.png)

这意味着，编译一次 pod，然后在任何地方使用它们。幸运的是，[@ fina analyst](https://github.com/finanalyst)做了一个模块编译缓存 pod 文件= > [Pod::缓存](https://github.com/finanalyst/pod-cached)。

上周五，我的导师， [@JJ](https://github.com/JJ) ，和我去看它是如何制作的，并解决了一些问题(你可以在这里看到我英俊的面孔)。

# 迷你单据集

Perl6::Documentable 上周发布了所有与处理相关的逻辑，所以我已经知道了几乎所有正在考虑生成 doc 站点的东西。

正因为如此，我更新了[迷你文档报告](https://github.com/antoniogamiz/mini-doc)中的文档集，在 README.md 中添加了一个特性检查列表(由 [@jj](https://dev.to/jj) 建议)，包含了该集中应该出现的所有东西。

# 关闭

蚂蚁就是这样！我知道这一周不是很有成效，但还是做了一些工作。本周我将致力于:

*   向 Perl6::Documentable 添加索引生成逻辑。
*   启动并有希望完成负责获取`Perl6::Documentable::Registry`并生成网站所需的 HTML 文件的新模块。