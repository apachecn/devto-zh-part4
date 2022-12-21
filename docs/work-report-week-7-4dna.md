# 工作报告，第 7 周。

> 原文：<https://dev.to/antoniogamiz/work-report-week-7-4dna>

### 新羽毛

我又来了一周！这个星期，我改进了 Perl6 的很多方面:

*   现在它有一个非常有用的`update`选项，可以只重新生成来自实际修改过的 pod6 文件的 HTML 文档。这是[文档的愿望清单](https://github.com/perl6/doc/issues/2668)中的一个特征。在这个特性之前，如果你想在 HTML 中看到一个变化，你必须重新生成一切，大约需要 2:30 分钟，再加上再次处理 pod 集合。现在，使用缓存和更新选项，只需要大约 10 秒钟就可以看到变化(其中一半时间用于重新处理集合)。

*   我还做了一些小的重构，以更好的方式分配职责。另外，你会发现一个新的`setup`选项。这个特性的目的是初始化生成文档的目录。需要设置什么？实际上，没有什么，但是如果您希望新生成的文档具有样式表、搜索功能等。，您需要下载必要的文件或自己提供。

*   一些小的错误修正，如:[前导空格文件](https://github.com/antoniogamiz/Perl6-Documentable/issues/43)或[正确检测子类](https://github.com/antoniogamiz/Perl6-Documentable/issues/34)。

*   CI 测试！这是一个重要的改变，因为我犯了一个可怕的错误！我没有测试该模块是否真的可以安装，它不能！感谢@ugexe 注意到这个并告诉我。我不知道你是否记得 [Perl6::LinkHealth](https://github.com/antoniogamiz/Perl6-LinkHealth) 和[迷你文档库](https://github.com/antoniogamiz/mini-doc)，但是它们也已经被集成到这个 CI 测试中，所以现在几乎所有的东西都被覆盖了:d

这就是我这个星期所做的全部工作，如果你想让我介绍任何你觉得有趣或有用的新特性，请告诉我！

## 下一步

嗯，接下来我该做什么？Perl6::Documentable 现在状态很好，所以我会继续修复 bug，做一般性的改进。

尽管如此，我也想用 [Pod::To::HTML](https://github.com/perl6/Pod-To-HTML) 和 [Pod::To::BigPage](https://github.com/perl6/perl6-pod-to-bigpage) 开始工作。需要做些什么？嗯，目前索引有三种不同的方式，所以如果我们能把它们统一成一种就好了。此外，最后两个模块使用不同的方法将 pod 文件转换为 html，因此使用重复的逻辑没有多大意义。我甚至可以只创建一个模块来连接两种功能。我仍然不知道，我还需要和我的导师们讨论这个问题！

## Perl6 好奇心

这几周我一直在想，写一篇文章来讲述我在特定的一周做了什么并不那么有趣，所以我将告诉你一些我发现有用和有趣的 Perl6 的特性。

今日:CLI！这一周，我不得不在 CLI 版本的 [Perl6::Documentable](https://github.com/antoniogamiz/Perl6-Documentable) 中添加不同的选项，我认为这有点乏味，但我大错特错了！与往常一样，Perl6 让您的生活变得更加轻松，并为您提供了一种非常简单的方法。

假设您想要创建自己的 CLI 程序，例如，一个再现`zef`命令的程序。首先，您应该创建一个包含该功能的新文件，姑且称之为`Perl6::Zef`。现在你需要创建一个新文件，在这里你将读取用户指定的参数，`Perl6::Zef::CLI`。

这个新文件应该是这样的: