# GSoC 报告。第六周

> 原文：<https://dev.to/antoniogamiz/gsoc-report-week-6-28i4>

## 首发来了！

我又来了一周！这个星期我在这个项目上取得了很大的进步！Perl6::Documentable 现在有了一个命令行版本，有很多选项可以定制构建过程或者只生成你想要的部分。

我还想发布这个模块的`1.0.0`版本`。为此，我在 GitHub 做了一个[项目，来管理相关问题，在那里你可以看到我这一周工作的详细报告。](https://github.com/antoniogamiz/Perl6-Documentable/projects)

但是，我为什么要发布呢？因为现在它只包含了当前的逻辑，没有任何变化(嗯，有一些，但它们不改变最终的结果)，包括错误。所以现在是时候修复这些错误，修改当前的行为，并开始跟踪这些变化了。当这些完成后，我们将拥有一个稳定的系统，在这个系统中我们将能够向[新的文档系统](https://github.com/perl6/doc/wiki)前进。

这些错误/问题在[这里](https://github.com/perl6/doc/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3Abuild+)和[这里](https://github.com/perl6/doc/issues?q=is%3Aissue+is%3Aopen+label%3Asite)描述。你可以看到它们的数量相当可观。我的意图是在升级初始版本时修复它们，发布几个补丁(`1.x.x`)。

此外，我已经在回购中启动了一个 [wiki](https://github.com/antoniogamiz/Perl6-Documentable/wiki) 来更深入地解释一些功能，因为在自述文件中我只添加了 API 文档。目前它只包含一篇关于如何初始化`Perl6::Documentable::Registry`以及你会在那里找到什么样的`Perl6::Documentable`对象的文章。我将用以下几页来完成它:

*   使用注册表:一些关于如何使用`lookup`或`grouped-by`来完全理解幕后发生的事情的例子。
*   属性:如果你看一下`Perl6::Documentable`，你会看到很多奇怪的属性，比如`categories`、`kind`、`subkinds`等等。在 API 文档中描述了它们是如何设置的，但没有描述它们的含义或为什么这样选择它们。

这就是本周的全部内容！此外，Perl 6 年度调查的结果已经发布[这里](https://github.com/perl6/p6survey/blob/master/processed-data/data-2018-2019.csv)，请查看！

### 感谢阅读

不要害羞，让我知道你对☺️.评论这个项目的看法