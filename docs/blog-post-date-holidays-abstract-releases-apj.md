# 博客帖子:日期::假期::摘要发布

> 原文：<https://dev.to/jonasbn/blog-post-date-holidays-abstract-releases-apj>

最近我收到了我的旧 Perl 发行版的问题报告[日期::假期::摘要](https://github.com/jonasbn/perl-date-holidays-abstract)。

这个 bug 很容易解决，只是发行版的一些元数据中的一个简单错误。我已经修复了所有问题，并发布了 0.08 版本。

感谢 Olaf Alders 报告了这个问题，尽管我自 2014 年以来没有发布过。

深入代码和发行版激发了我内心的童军，我决定再清理一些。我调整了我的开发环境，并用编辑器内的林挺修复了一些小问题。如果对这个话题感兴趣，请看我早先的帖子:[《博文:Bugs！？—“静态分析拯救”](https://dev.to/jonasbn/bugs---static-analysis-to-the-rescue-2m17)和[“博客文章:Perl 中的静态分析——或者另一种策略”](https://dev.to/jonasbn/static-analysis-in-perl---or-yet-another-policy-m98)。

总之，正如我在之前的博客文章中所描述的:[“TIL:Todo Tree 一个漂亮的 VScode 扩展”](https://dev.to/jonasbn/til-todo-tree-a-nifty-vscode-extension-16j5)我现在可以很容易地在我的代码库中找到 Todo。TODO 文件是一种有时会引起误解的表示，它在历史上一直伴随着开源软件的发布，当它们作为焦油球发布时。今天，当您可以更容易地访问中央存储库和共享代码和信息时，我更喜欢指向问题列表或类似的内容，而不是维护伴随源代码的文本文件。插件指向了我的 TODO 文件。

```
TODO file for Date-Holidays-Abstract

$Id: TODO 1712 2007-02-13 21:42:34Z jonasbn $

- Create a proper test for the use part

- Implement some breaking tests using Test::Exception

- Move Abstract.pm to root directory this is a one-module distribution, no
  need for the lib/ directory 
```

Enter fullscreen mode Exit fullscreen mode

我甚至解决了一些 TODOs，并提高了单元测试覆盖率。这个发行版非常简单，并且拥有 100%的覆盖率，但是我想实现一些测试场景来证明代码工作正常。

该发行版基本上是一个抽象类，旨在支持向`Date::Holidays::`名称空间和结构添加日历。

这个测试实现起来有点滑稽，因为我想触发一个编译时错误，并使用一个测试来捕获这个错误，这通常是在运行时环境中进行的。

这个解决方案被证明是基本的，因为我只需要做与我多次使用的惯用构造相反的事情，动态加载一个 Perl 组件，如果失败就抛出一个异常。我编写了相同的构造，知道我的测试组件会触发异常，捕获异常并断言其内容。

```
#!/usr/bin/env perl

use strict;
use warnings;

use English qw(-no_match_vars);
use FindBin qw($Bin);
use lib ("$Bin/../t", 't');

use Test::More;
use Test::Fatal qw(dies_ok);

## no critic ( ProhibitStringyEval ProhibitComplexRegexes RequireDotMatchAnything RequireLineBoundaryMatching RequireExtendedFormatting)

local $EVAL_ERROR = q{}; # protect existing $@ ($EVAL_ERROR)
my $rv = eval 'use Example::Abstractionless';

diag("Diagnostics ($rv): ", $EVAL_ERROR);

like($EVAL_ERROR, qr/Class Example::Abstractionless must define is_holiday, holidays for class Date::Holidays::Abstract at/, 'abstraction not implemented, we observe a compilation error');

done_testing(); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，我就可以发布 0.09 版了，我可以去掉 TODO 文件了。正如我所概述的，我可以在我的编辑器中看到我所有的 TODO 注释，此外我还使用了 [probot](https://probot.github.io/) ，所以当我提交我的更改时，probot 基于新的 TODO 注释向[报告了一个问题](https://github.com/jonasbn/perl-date-holidays-abstract/issues/4)。

```
# TODO: we should get perltidy going so this policy can be reenabled 
```

Enter fullscreen mode Exit fullscreen mode

此外，无处不在的 Perl 发行版补丁程序和 CPAN 贡献者 Mohammad S Anwar 提交了一份 PR 文件，对文档进行了一些更正。我得到了合并，并发布了 0.10。

感谢穆罕默德·S·安瓦尔让我保持警觉。

这个发行版很老了，可能没怎么用，但它确实存在，而且已经成为了`Date::Holidays::`项目的一部分，我在之前的博客中已经多次提到过。我写的大部分代码都是为自己写的，但是如果有人想从我的实验、实现和*错误中学习，我会很乐意分享。我偶尔会收到 PRs，有时会收到一个全新的[日期::假期](https://jonasbn.github.io/perl-date-holidays/)日历，这让我很高兴，但作为一名开源软件作者，我主要得到的是修补、试验和学习的自由。Date::Holidays 是使用适配器模式的练习，`Date::Holidays::Abstract`是使用抽象类的练习，*堂兄*[Date::Holidays::Super](https://metacpan.org/pod/Date::Holidays::Super)是超类。*

不要害怕发布一些东西，维护一个开源发行版将会让你接触到很多与软件开发相关的学科，比如:

*   文件写作
*   单元测试、CI 和 QA
*   释放和运输
*   bug 分类
*   当然还有编码

实践以上所有这些，并且公开地去做，会对你的职业和教育有好处。您可以熟悉其他领域，这些领域可能不是您的工作或工作描述的一部分，但有了理解，可以更容易地与 DevOps 人员、测试人员、技术作者和支持者等互动。

无论如何，我对我的开源事业非常重视，我对贡献也非常重视，所以我决定在我的[贡献指南](https://github.com/jonasbn/perl-date-holidays-abstract/blob/master/CONTRIBUTING.md)中添加以下内容，以感谢贡献者:

> 致谢和提及
> 
> 请注意，所有的贡献都得到了承认，贡献者是通过可用的身份来提及的，如果你作为一个贡献者不希望被明确提及，请指出这一点，公关机制不能被忽视。
> 
> 如果您希望以 GitHub handle 或类似方式之外的特定方式被提及，请说明这一点，我们将尝试提供便利，仅限于可用的方式。

因为我所能提供的只有感谢和由衷的感激。