# 常见 Git 问题的 4 个有用解决方案

> 原文：<https://dev.to/jacobherrington/4-useful-patterns-in-git-19ac>

<sup>封面图片:[黑色和银色的笔记本电脑放在圆形棕色木桌上](https://www.pexels.com/photo/black-and-silver-laptop-computer-on-round-brown-wooden-table-1181243/)作者[克里斯蒂娜·莫里洛](https://www.pexels.com/@divinetechygirl)</sup>
如今，大多数开发人员都在使用某种形式的版本控制，而当今最流行的版本控制系统就是 git。

我写过几篇方便的 git 文章:

*   [10 个节省时间和保持理智的窍门](https://dev.to/jacobherrington/10-git-tricks-to-save-your-time-and-sanity-289h)
*   [Git 二等分很容易](https://dev.to/jacobherrington/git-bisect-is-easy-44ol)
*   [Hunky Git 快速指南](https://dev.to/jacobherrington/a-quick-guide-to-hunky-git-49no)
*   [你应该知道的另外 10 个 Git 技巧](https://dev.to/jacobherrington/10-more-git-tricks-that-you-should-know-3bbp)

这一篇将是那些文章的延续，更侧重于实用主义。在之前的文章中，我分享了你可以用 git 做的有趣的事情。在这篇文章中，我将关注每个开发人员应该知道如何做的事情。

### 1。将更改添加到上次提交

修改最后一次提交对于那些忘记准备变更的人(也就是所有使用过 git 的人)来说很方便。

这真的很简单。您所要做的就是暂存您忘记暂存的文件，然后使用`git commit --amend`命令。

假设我忘记了对自述文件进行修改，我会这样做:

```
git add README.md
git commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

我会被提示修改提交消息，然后:Tada！README.md 更改已添加到上次提交中。

### 2。将最后一次提交拆分为多次提交

假设你有一个不同的问题。如果您不小心暂存了某个内容，然后在打算提交两次时提交了一次。

您所需要做的就是使用`git reset`命令:
返回一次提交

```
git reset HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

然后使用`git add`来展示你的改变(我推荐使用`git add --patch`，[，我写了一篇关于它的文章，如果你不熟悉](https://dev.to/jacobherrington/a-quick-guide-to-hunky-git-49no)，然后提交:

```
git add --patch
git commit -m "This is the first commit" 
```

Enter fullscreen mode Exit fullscreen mode

然后根据需要多次重复该过程:

```
git add --patch
git commit -m "This is the second commit" 
```

Enter fullscreen mode Exit fullscreen mode

一次承诺变成了多次承诺。💥

### 3。将以前的一些提交压缩成一个提交

每个开发人员都应该知道如何挤压提交。如果你对开放源码做出了贡献，并且已经提交了一些多余的或者措辞不当的提交消息，你可能会被要求这么做。

我将分享几种方法，一种使用`git rebase`，另一种不使用。

如果没有`git rebase`，你可以很容易地将最后几个提交压缩成一个。首先，您将希望使用`git reset`命令在您想要压缩的提交中向后移动。

您可以通过将`HEAD~x`传递给`git rebase`命令来实现这一点，其中`x`是您想要挤压在一起的提交数量。

例如，如果您想将前面的三次提交压缩成一次提交，您可以运行下面的命令:

```
git reset HEAD~3 
```

Enter fullscreen mode Exit fullscreen mode

如果您检查日志，您会注意到您现在比之前的位置落后了三次提交。

一个快速的`git log`将会显示来自前三次提交的所有更改仍然存在于您的机器上，但是现在没有被暂存。

为了将它们压缩到一个提交中，您需要做的就是将它们分段并提交:

```
git commit -am "Some commit message" 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！🎉你把三次提交压缩成了一次！

或者，您可以将`git rebase`与交互标志一起使用。看起来应该是这样的:

```
git rebase -i master 
```

Enter fullscreen mode Exit fullscreen mode

用您最终将合并的分支替换 master。

`git rebase -i`会让你进入一个文本编辑器，看起来像这样:

```
pick 1eabc17a8 Add data-attributes to container elements
pick 90c58b487 Fix react-dom warning

Rebase 5f5e140ea..90c58b487 onto 5f5e140ea (2 commands)

Commands:
p, pick <commit> = use commit
r, reword <commit> = use commit, but edit the commit message
e, edit <commit> = use commit, but stop for amending
s, squash <commit> = use commit, but meld into previous commit
f, fixup <commit> = like "squash", but discard this commit's log message
x, exec <command> = run command (the rest of the line) using shell
b, break = stop here (continue rebase later with 'git rebase --continue')
d, drop <commit> = remove commit
l, label <label> = label current HEAD with a name
t, reset <label> = reset HEAD to a label
m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
.       create a merge commit using the original merge commit's
.       message (or the oneline, if no original merge commit was
.       specified). Use -c <commit> to reword the commit message.

These lines can be re-ordered; they are executed from top to bottom.

If you remove a line here THAT COMMIT WILL BE LOST.

However, if you remove everything, the rebase will be aborted.

Note that empty commits are commented out 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未执行过 rebase，这可能有点吓人，但实际上非常简单。

为了压缩这两个提交，只需将第二行中的单词`pick`替换为`squash` :

```
pick 1eabc17a8 Add data-attributes for styling checkout
squash 90c58b487 Fix react-dom warning; non-critical, but annoying 
```

Enter fullscreen mode Exit fullscreen mode

保存文件(如果您在 Vim 中，这是用`:wq`完成的)，然后您将进入一个文本编辑器来编写提交消息。当你高兴的时候，保存提交，你就完成了！

呜哇！🤠现在，您已经看到了两种不同的方式来挤压提交。

你可能想知道为什么你会选择使用`rebase`当第一种方法是如此容易，这是可以理解的。使用`git rebase`可以让你对大的变化有更多的控制，我推荐你在遇到这些问题时使用它。

### 4。从另一个分支获取最新的更改

当您使用特征分支时，这可能是您一天要做多次的事情！

有两种方法可以做到这一点。

最常用的方法是`git merge`命令。您所要做的就是使用您想要从中获取变更的分支的名称运行`git merge`命令。例如，如果你从师父那里得到最新的变化:

```
git merge master 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个新的提交，称为合并提交。合并提交基本上只是一个发生了合并的指示，在您的 git 历史中有这些提交没有任何问题(在我看来)。

然而，有些人不喜欢合并提交出现在他们的存储库的历史中。对于那些人，你可以用`git rebase`赶上另一个分支。这是典型的我喜欢让我的特性分支赶上主的方式:

```
git rebase master 
```

Enter fullscreen mode Exit fullscreen mode

只要没有什么奇怪的事情发生，这将在主分支的顶部无痛苦地重放您的更改，在不创建合并提交的情况下赶上您。

🤗

### 还有更多...

这些天来，我写了很多文章，运行了一个 T2 播客，并开始发送一个 T4 时事摘要，介绍我听到的所有精彩故事。

你也可以在 Twitter 上关注我，在那里我制作一些愚蠢的迷因，谈论如何成为一名开发者。