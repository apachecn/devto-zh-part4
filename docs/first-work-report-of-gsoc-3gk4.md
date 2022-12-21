# GSoC 第一份工作报告！

> 原文：<https://dev.to/antoniogamiz/first-work-report-of-gsoc-3gk4>

我回来了，如我所承诺的！是时候谈谈我这段时间都在做什么了。自编码期开始以来，已经过去了三个星期，自那时以来已经做了一些工作:

# Perl6::LinkHealth [回购](https://github.com/antoniogamiz/Perl6-LinkHealth)

这是我的第一个模块！我试着做了一个基本的工具来检查一些文件是否停止生成。它需要改进，还没有完成，但我的导师建议我暂时搁置这个项目。

# 迷你文档库[第 2529 期](https://github.com/perl6/doc/issues/2529)

在 Perl6 文档报告的 [doc 目录](https://github.com/perl6/doc)中可以看到，大约有 383 个 pod 文件。处理它们并建立文档站点需要相当长的时间(大约 20 分钟)。这导致了一个问题:如果您想要对构建过程进行任何更改，首先您需要遵循这些简单的步骤:

1.  对变更进行编码。
2.  建立网站等待 20 分钟。
3.  是的，在这一步，你仍然在等待。
4.  看到你忘了一个分号，回到步骤 1。

相当令人沮丧，不是吗？因此，为了改变这一点，正如在[第 2529 期](https://github.com/perl6/doc/issues/2529)中所讨论的，一个迷你文档库将会很有帮助。所以我在这里创建了它，但是，一旦环境建立起来，我开始搜索 pod6 文件的正确的自包含子集，我意识到我不知道它需要包含什么！因此，为了发现什么是必要的，我做了以下工作:

# 剥离 Perl6::可记录的[第 1937 期](https://github.com/perl6/doc/issues/1937)

为了知道我们需要什么，首先我们需要知道它被用来做什么以及如何被使用。那么，pod 文件是如何处理的，在哪里处理？

首先它们被读取和解析，然后我们在一个数组中得到一个`Pod::*`对象的数据结构。这些对象包含写在 pod 文件中的文本，但是要生成索引并拥有一个搜索系统，还需要一个附加层。那就是`Perl6::Documentable`和`Perl6::Registry` ( [源代码](https://github.com/perl6/doc/tree/master/lib/Perl6))参与的地方。`Documentable`对象是被记录的 pod 文件的任何一部分，但是，记录的是什么呢？几件事:

*   Pod 文件: [doc 目录](https://github.com/perl6/doc/tree/master/doc)中的每个 pod 文件都由一个具有以下属性的`Documentable`对象表示:
    *   `$name`:默认设置为文件名。如果 pod 代表一种类型，那么它被设置为文件名的最后一个单词。否则设置为`=TITLE`的内容。
    *   `$url`:设置为`/$kind/$link`。`$kind`是分配 pod 文件的文件夹的名称，可以将`$link`设置为您想要的值，在`=begin pod`后面加上`link<your-link>`。如果没有，那么它被设置为文件名。
    *   `$pod`:包含有数据的`Pod::*`对象的数组。
    *   `$summary`:`=SUBTITLE`的值。
    *   `$kind`:pod 文件所在文件夹的名称(`Language`、`Type`或`Programs`)。
    *   `$subkinds`和`$categories`:如果是类型，设置为从`Perl6::Typegraph`获得的值。如果不是，那么`$subkind`被设置为`class`，并且`$categories`不被设置。
    *   `pod-is-complete`:设置为`true`，因为它们代表整个 pod 文件。

当所有这些都设置好后，新的`Documentable`对象被添加到`Perl6::Registry`中，确切地说，它是一个注册表，所有的`Documentable`对象都存储在这里以备后用。

接下来需要做的事情是搜索要索引的定义。这是这个过程的一个关键部分，因为这个定义将被用来创建 TOCs，一个站点和内部链接的搜索文件。让我们开始解释什么是定义:定义是一个头，更准确地说，是一个`Pod::Heading`对象。它们大概是这样的:`=head2 Some text`(标题级别无所谓)。但是你可以想象，并不是所有的标题都是有效的定义。有两种不同类型的有效类型:不明确的和明确的。你可以在这里查看更多关于他们[的信息。每个新找到的定义都存储在一个`Documentable`对象中，并添加到注册表中。](https://github.com/antoniogamiz/Perl6-Documentable#method-parsedefinitionheader)

但还有更多:参考。引用是一个`X<>`元素。所有的`X<>`元素都被作为引用处理，存储在一个`Documentable`对象中，并添加到注册表中。

所有这些工作都是由三个不同的功能完成的:

*   [过程-容器-源](https://github.com/perl6/doc/blob/612fcacea0f1f56dfc4b8c8909fdbc6c2d34f9ac/htmlify.p6#L315)
*   [查找定义](https://github.com/perl6/doc/blob/612fcacea0f1f56dfc4b8c8909fdbc6c2d34f9ac/htmlify.p6#L543)
*   [查找参考资料](https://github.com/perl6/doc/blob/612fcacea0f1f56dfc4b8c8909fdbc6c2d34f9ac/htmlify.p6#L369)

如果你看一下这些函数，你会发现它们包含了全局变量。此外，代码可以是并行的(当前是并行的，但是线程数设置为 1)。而且，你可能知道，全局变量和并行性，需要监视器或一些类似的排除机制。看起来监视器没有像预期的那样工作(由于这个原因，线程数被设置为 1)。为了解决这个问题，我把这里描述的所有逻辑都移到了`Perl6::Documentable`类，这里没有任何全局变量，所以不需要监视器。你可以点击查看新模块[。](https://github.com/antoniogamiz/Perl6-Documentable)

# Perl6::Typegraph [第 2573 期](https://github.com/perl6/doc/issues/2573)

我旋转这个模块来测试和记录它。另外，我们想去掉`type-graph.txt`文件，通过自省获得所有必要的信息。这个[文件](https://github.com/perl6/doc/blob/master/lib/Perl6/TypeGraph/Viz.pm6)也应该添加到那个模块中。

# Pod::Load [回购](https://github.com/JJ/p6-pod-load)

这个模块将用于读取文档中的每个 pod 文件，所以我一直在使用它，并发现了一些错误。

# [esLibre19](https://eslib.re/2019/)

三天前是#esLibre19！一个关于开源的大会，在那里我主持了一个关于 Perl 和 Perl6 的 devroom！

# 接下来是什么？

本周我想重点谈几件事:

*   出版并润色`Perl6::Documentable`。
*   分拆`Pod::Convenience`，将更名为`Pod::Utilities`。此外，在回购中发现的与此主题相关的所有分散功能都将被移到那里以保持一致性。
*   讨论将读取和初始化 pod 和注册表的逻辑移动到哪里(这不是小事，因为在接下来的几周内将会创建一个缓存系统，并且该逻辑将会使用它)。
*   创建`Perl6::RegistryToDoc`模块，`htmilify.p6`的其余逻辑将被移动到该模块中。

我没有解释几件事情，但是如果你去每个模块的仓库，你会发现一个解释所有必要的文档！

这就是本周的全部内容！下周见，希望:d。