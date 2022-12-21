# 用更好的提交消息增强您的 Git 历史

> 原文：<https://dev.to/bhekanik/supercharge-your-git-history-with-better-commit-messages-32fk>

这里是我学到的一些关于编写 Git 提交消息的经验，我认为每个开发人员都应该学习和实现。如果做得好，它将增加您的 Git 历史的有用性，并在维护代码时为您的团队节省大量时间。

## TL；速度三角形定位法(dead reckoning)

```
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. The first line is treated as the subject of the 
commit and the rest of the text as the body. The blank line 
separating the summary from the body is critical (unless you omit 
the body entirely); various tools like `log`, `shortlog` and 
`rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too
 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789 
```

## 为什么好的提交消息很重要

一个项目的长期成功依赖于它的可维护性，维护人员没有比项目日志更强大的工具了。如果做得好，Git 历史可以是一个活的、准确的和可搜索的记录，记录了项目的代码是如何以及为什么是这样的。它将使审查其他人的提交和拉请求变得更容易，而不需要咨询他们，因为它使理解为什么几个月或几年前发生的事情不仅是可能的，而且是有效的。

有效地记录代码进展的能力与编写干净的代码或可读的测试一样重要。尽管代码本身应该是自文档化的，但它本身并没有说明代码为什么是现在这个样子，或者它是如何形成的。一个精心制作的 Git commit 消息可以向其他开发人员(以及您未来的自己)传达关于变更的上下文。diff 会告诉您发生了什么变化，但是如果做得好，commit 消息会正确地告诉您原因。彼得·哈特勒说得很好:

> 重建一段代码的上下文是一种浪费。我们不能完全避免它，所以我们应该努力尽可能地减少它。提交消息可以做到这一点，因此，提交消息显示开发人员是否是一个好的合作者。

这里有一个恶性循环:如果提交历史是非结构化和不一致的，没有人会花太多时间使用或处理它。并且因为它没有被使用或照顾，它变得更加无结构和不一致。

值得花时间学习如何适当地关注您的 Git 历史。一开始可能会很麻烦，但很快就会成为习惯，最终成为所有参与者的骄傲和生产力的源泉。

## 原子提交

在开始讨论如何编写一个好的提交消息之前，让我们先来看看进行原子提交的重要性。

大型提交可能难以阅读，尤其是当它们包含不相关的更改时。考虑如何拆分一个大的提交可能很困难，但是一个有用的方法是考虑每个提交的目的，就像考虑编写完成任务的清单一样。每个人都应该做一件特定的事情，如果你发现自己想在提交消息中使用“and ”,那么你可能提交了太多，把它分开。

可以将原子提交看作是交付价值的最小数量的代码更改——无论是整理现有代码还是引入新的(小)特性。让我们看一个例子:

```
$ git log
commit 42e769bdf4894310333942ffc5a15151222a87be
Author: Sansa Stark <sansa@winterfell.com>
Date:   Fri Jan 01 00:00:00 1982 -0200

    Take the Iron Throne at King's Landing

61 files changed, 937 insertions(+), 81 deletions(-) 
```

在本例中，这一次提交中有许多更改。从表面上看，所有这些变化似乎都是相互关联的。但是，如果您要分解这些变化，您会对正在发生的事情有更好的了解，例如:

```
5feaccf Defeat Daenerys
c829cbc Usurp Cersei
a6455f8 Get hitched
b529f6d Leverage on the Stark name
b5bb6e4 Pander to Littlefinger for strategic reasons 
```

不仅每次提交都变小了，它还为您提供了一个产生最终结果的更好的故事。如果用户想要挑选特定的更新或特性，如果提交是原子性的，他们可以更容易地做到。

原子提交的正式定义是，“一个不可约的有用的变更集”。定义中的有用性非常重要。

现在来看文章的主要部分。

## 编写一个伟大的 Git 提交消息

我相信我们都同意，当没有约定，每个人都做自己的事情时，Git 历史通常变得毫无用处。为了创建一个有用的修订历史，团队应该首先阐明他们对提交消息的样式、内容和元数据的约定。重要的是消除对提交消息应该是什么样子的猜测，并使其尽可能简单。最终的结果将是一个非常一致的日志，不仅读起来很愉快，而且确实会定期被阅读。

幸运的是，对于什么是好的、有用的 Git 提交消息，已经有了很好的约定。没有什么你需要重新发明的。只要遵循下面的指导方针，你就能像专业人士一样投入。

### 1。将主题行限制在 50 个字符以内

50 个字符不是硬性限制，而是经验法则。让主题行保持这样的长度确保了它们的可读性，并迫使作者思考一下最简洁的方式来解释正在发生的事情。

提示:如果你很难总结，你可能一次做了太多的改变。争取原子提交(如上所述)。

大多数 git 工具都完全知道这个约定。如果超过了 50 个字符的限制，它们会发出警告，并用省略号截断任何超过 72 个字符的主题行。因此，争取 50 个字符，但考虑 72 个字符的硬性限制。

### 2。大写主题行

这听起来很简单。所有主题行都以大写字母开头。例如:

```
Make a cup of coffee 
```

代替

```
make a cup of coffee 
```

### 3。不要以句号结束主题行

主题行中不需要尾随标点。此外，当你试图将它们保持在 50 个字符或更少时，空间是宝贵的。示例:

```
Make a cup of coffee 
```

代替

```
Make a cup of coffee. 
```

### 4。在主题行中使用祈使语气

祈使语气的意思是“说或写的好像在发出命令或指示”。几个例子:

*   冲一杯咖啡
*   打开电脑
*   观看 YouTube 视频

祈使句听起来可能有点粗鲁；这就是为什么我们不常使用它。但是它非常适合 Git 提交主题行。其中一个原因是，每当 Git 代表您创建一个 commit 时，它本身就会使用这个命令。

例如，使用`git merge`时创建的默认消息为:

```
Merge branch 'newfeature' 
```

当使用 git revert 时:

```
Revert "Add the thing with the stuff"

This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d. 
```

因此，当您在命令中编写提交消息时，您遵循的是 Git 自己的内置约定。

这样写一开始会有点别扭。我们更习惯于用陈述语气说话，这完全是在报告事实。这就是为什么提交消息最终通常是这样的:

*   修复了 bug
*   更改按钮的行为

有时提交消息被写成对其内容的描述:

*   更多损坏的修复
*   可爱的新 API 方法

为了消除任何困惑，这里有一个简单的规则，每次都要做对。一个格式正确的 Git 提交主题行应该总是能够完成下面的句子:

*   如果应用，此提交将" ***你的主题行此处"***

例如:

*   如果应用，该提交将" ***重构子系统 X 以提高可读性"***
*   如果应用，该提交将" ***更新入门文档"***

请注意，这对于其他非命令式表单是不起作用的:

*   如果应用的话，这个提交将会修复*的 bug*
**   如果应用的话，这个提交将" ***改变按钮"*** 的行为*

 *记住:只有在主题行中使用祈使句才是重要的。写正文的时候可以放宽这个限制。

### 5。用一个空行将主题和正文分开

从 git 提交联机帮助页:

> 虽然不是必需的，但是在提交消息的开头用一行简短的(少于 50 个字符)总结更改，然后是一个空行，然后是更详细的描述，这是一个好主意。提交消息中第一个空行之前的文本被视为提交标题，该标题在整个 Git 中使用。例如，Git-format-patch(1)将提交转换成电子邮件，它在主题行上使用标题，在正文中使用提交的其余部分。

首先，不是每个提交都需要主体和主体。有时候一行就够了，尤其是当变化如此简单以至于不需要更多的上下文时。例如:

```
Fix typo in introduction to user guide 
```

没有更多的需要说；如果读者想知道打字错误是什么，他们可以简单地看一下修改本身，即使用`git show`或`git diff`或`git log -p`。

如果您在命令行提交类似这样的东西，很容易使用-m 选项来 git commit:

```
$ git commit -m "Fix typo in introduction to user guide" 
```

然而，当提交需要一些解释和上下文时，您需要编写一个主体。例如:

```
Forget that the final GOT season ever existed

The battle of winterfell turned out to be a major dissappointment 
and did not live up to the hype. This commit deletes all evidence 
that the final season ever happened, we prefer to just wonder how
things were going to end that accept how they actually ended. 
```

使用-m 选项编写带主体的提交消息并不容易。你最好用合适的文本编辑器来写这条信息。

浏览日志时，主题和正文的分离是值得的。以下是完整的日志条目:

```
$ git log
commit 42e769bdf4894310333942ffc5a15151222a87be
Author: kit harington <kit.harington@got.com>
Date:   Fri May 10 00:00:00 2019 -0200

    Forget that the final GOT season ever existed

    The battle of winterfell turned out to be a major dissappointment 
    and did not live up to the hype. This commit deletes all evidence 
    that the final season ever happened, we prefer to just wonder how
    things were going to end that accept how they actually ended. 
```

现在是`git log --oneline`，它只打印出主题行:

```
$ git log --oneline
42e769 Forget that the final GOT season ever existed 
```

Git 中还有许多其他上下文，主题行和正文之间的区别是有用的，但是如果中间没有空行，它们都不能正常工作。

### **6。在 72 个字符处换行**

Git 不会自动换行。当您编写提交消息的正文时，您必须注意它的右边距，并手动换行。

建议使用 72 个字符，这样 Git 就有足够的空间来缩进文本，同时仍然保持所有内容不超过 80 个字符。

一些 Git 工具和文本编辑器已经为您完成了这项工作。

### 7。用身体来解释什么和为什么以及如何

来自比特币核心的这一提交是一个很好的例子，解释了什么发生了变化以及为什么会发生变化:

```
commit eb0b56b19017ab5c16c745e6da39c53126924ed6
Author: Pieter Wuille <pieter.wuille@gmail.com>
Date:   Fri Aug 1 22:57:55 2014 +0200

   Simplify serialize.h's exception handling

   Remove the 'state' and 'exceptmask' from serialize.h's stream
   implementations, as well as related methods.

   As exceptmask always included 'failbit', and setstate was always
   called with bits = failbit, all it did was immediately raise an
   exception. Get rid of those variables, and replace the setstate
   with direct exception throwing (which also removes some dead
   code).

   As a result, good() is never reached after a failure (there are
   only 2 calls, one of which is in tests), and can just be replaced
   by !eof().

   fail(), clear(n) and exceptions() are just never called. Delete
   them. 
```

想想作者花时间在此时此地提供这种背景，为同事和未来的提交者节省了多少时间。如果他不这样做，那么如果他离开，这种背景可能会永远消失。

集中精力弄清楚你做出改变的原因，改变前事情的运作方式(以及有什么问题)，现在的运作方式，以及你为什么决定用你的方式解决问题。在大多数情况下，您可以省略有关如何进行更改的细节。在这方面，代码通常是自解释的(如果它太复杂，需要解释，那么使用源代码注释)。

试着从维护代码的开发人员的角度来看待提交。当查看您的代码变更时，他们可能会问什么问题？什么可能不会立即显而易见？

感谢你的未来维护者可能就是你自己！

## 关闭提示

### 分享前修改历史

当你写代码的时候，你一定会改变方向，甚至会犯一些错误，比如输入错别字或者 bug。

```
324d079 Fix typo in enrolment flash message
3a85f77 Only display enrol button for users who can enrol
4cc4778 Allow users to enrol on courses 
```

在这个例子中，开发人员在他们的第一次提交中引入了一个错别字，他们在后来的提交中修复了这个错别字。

在您共享提交历史记录之前，重要的是要考虑哪些信息对阅读历史记录的其他人是有用的。你不应该认为你的 Git 历史是你一步一步工作的“真实”日志。正如我们重构代码一样，我们应该在与他人分享之前重构我们的提交，这样我们就不会弄乱历史。

Git 拥有强大的工具，可以简单地对你的提交进行重新排序、改写和重构，直到它们尽可能清晰地讲述故事。使用 Git 的交互式基础功能来讲述一个更清晰的故事:

```
$ git rebase -i

3a85f77 仅向可以注册的用户显示注册按钮
 773e345 允许用户注册课程 
```

### 
  
读亲 Git

看看这本 [Pro Git](https://git-scm.com/book/en/v2) 的书，它可以在网上免费获得。

## 鸣谢

这篇文章很大程度上来源于 Chris Beams 的[文章](https://chris.beams.io/posts/git-commit/)，Seb Jacobs 的[文章](https://about.futurelearn.com/blog/telling-stories-with-your-git-history)，Sebastian Feldmann 的[演讲](https://www.youtube.com/watch?v=f-Br8cud2eI)以及我自己维护项目的经验，而这些项目的历史并不那么有用。*