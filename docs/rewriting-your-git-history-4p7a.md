# 重写你的(git)历史

> 原文：<https://dev.to/bmpickford/rewriting-your-git-history-4p7a>

所以你可能知道很多 git 的基础知识，但是如果你已经使用了一段时间，你可能会遇到这样的情况，你想改变一个已经提交的提交。一些人认为这是不好的做法，其他人说有不干净的犯罪历史是不好的。虽然我可能更倾向于后者，但这篇文章只是关于如果你想改变一些已经提交的提交，你可以使用的工具

## 1。更改最近的提交消息

```
git commit --amend 
```

如果你使用 git 已经有一段时间了，你可能已经知道这个了。如果您发现了一个类型或者想要更改您的提交消息布局，它通常用于更改您最近的提交消息。也可以通过在 amend 后面添加`--reset-author`来改变作者

# 交互式 rebase

对于这些例子，我将使用我创建的 3 个提交(见下文),所有这些例子首先涉及交互式 rebase 命令:

```
git rebase -i HEAD~3 
```

这将使您看到一个类似如下的终端输出:

```
pick 74c349f [1] Dummy commit 1
pick f490852 [2] Dummy commit 2
pick 717eaa6 [3] Dummy commit 3

# Rebase b8d17a8..717eaa6 onto b8d17a8 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message. 
```

我将把它作为其余例子的基础

## 2。挤压提交

如果您有一些相关的提交可以合并成一个更大的提交，或者在某些情况下，用于使代码审查更容易，压缩提交是有用的

要压缩提交，只需将提交 2 和 3 中的单词`pick`替换为`s`，将它们合并为提交 1。
例如:

```
pick 74c349f [1] Dummy commit 1
s f490852 [2] Dummy commit 2
s 717eaa6 [3] Dummy commit 3 
```

这将引导您进入新的提交，您可以在保存并退出提交消息后编辑/合并提交消息。保存提交消息后，您应该能够`git log`并在一次提交中看到您的所有更改

## 3。更改旧的提交消息(改写)

如果我想改变提交 3 的消息，我会做:

```
pick 74c349f [1] Dummy commit 1
r f490852 [2] Dummy commit 2
pick 717eaa6 [3] Dummy commit 3 
```

然后像平常一样继续`git commit --amend`

## 4。放弃提交

如果您要放弃提交，请格外小心内容，并考虑改为压缩。但是，如果您真的想删除提交，只需使用 drop 命令。例如

```
pick 74c349f [1] Dummy commit 1
d f490852 [2] Dummy commit 2
pick 717eaa6 [3] Dummy commit 3 
```

这将从您的提交历史记录中删除`[2] Dummy commit 2`及其更改

## 5。编辑提交

如果您想要添加或删除以前提交的文件/更改，您可以使用编辑功能。例如，如果您添加了一个文件，但它实际上应该属于前面的提交，您应该:

```
pick 74c349f [1] Dummy commit 1
e f490852 [2] Dummy commit 2
pick 717eaa6 [3] Dummy commit 3 
```

然后，您可以随意添加/删除文件。完成后，使用`git commit --amend`命令，后面跟着一个`git rebase --continue`

这也可以用来将提交一分为二。举例来说，如果您遵循上面的编辑步骤，并且想要在它之前添加一个新的提交，您将执行上面的操作，然后:

```
git reset HEAD^ 
```

从这里您可以添加您想要的文件，并创建新的提交。完成后，执行`git rebase --continue`命令

* * *

感谢您的阅读，我希望这能理清一些交互式的 rebase 功能