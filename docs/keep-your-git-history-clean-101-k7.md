# 保持你的 Git 历史干净- 101

> 原文：<https://dev.to/manuelsidler/keep-your-git-history-clean-101-k7>

> [JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/cleaning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的封面照片

我使用 Git 已经有几年了，不可否认，干净的提交历史对我来说并不总是那么重要。正如我们从《回到未来》中所知，重写历史可能会带来一些不好的后果。我只能假设这就是为什么这是一个如此可怕的话题。在这篇文章中，我想分享一些基本的命令，这将帮助你保持你的 Git 历史清白，带走你回到未来的创伤；-)

## 先做第一件事

这篇文章中的所有命令都会生成新的提交散列，因此会使你的分支偏离原点。这意味着您必须使用`git push --force`或`git push -f`
进行强制推送，以覆盖 remote 上的历史。这反过来意味着:永远不要更改共享分支上的 git 历史。
如果远程分支包含比本地分支更新的提交，有一个更安全的选项甚至会拒绝强制推送:
`git push --force-with-lease`

## 场景 1:在最后一次提交时添加内容

大家都遇到过这样的情况。您将您的更改添加到临时区域，提交它并等待绿色构建。不幸的是，你的构建失败了。当然，你忘了添加文件 x。接下来是什么？将文件 x 添加到暂存区，提交一条类似“愚蠢的我忘记添加文件 x 了”的消息，然后停止！让我给你介绍一下我们的第一个命令:
`git commit --amend`
这个命令将你的修改从暂存区添加到最后一次提交，并给你机会修改你的最后一次提交消息。你也可以用一个简单的命令:
`git commit --amend -m "new message"`
如果你不想改变你最后的提交信息，你可以只添加 *- no-edit* 参数:
`git commit --amend --no-edit`
这样就不会再有“愚蠢的我忘记了”的提交了！

## Git 交互式 rebase

在接下来的几个场景中，我们将使用交互式 git rebase。这个工具帮助我们修改历史上更久远的变更。您只需使用下面的命令启动一个交互式 rebase:
`git rebase -i <base>`
，其中`<base>`表示您想要重写多远以前的历史。
例如，重写最后三个提交:
`git rebase -i HEAD~3`
另一个例子，重写历史到一个特定的提交:
`git rebase -i 63a2356`
该命令现在向您显示您配置的编辑器中的提交和选项:

```
pick bcfd87e add sql scripts for database
pick 9fb0b9c prevent users from changing their email
pick e0b46b9 cleanup web config

# Rebase dfef724..e0b46b9 onto dfef724 (3 commands)
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
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out 
```

Enter fullscreen mode Exit fullscreen mode

让我们进入第一个重定基础的场景。

## 场景 2:在你的历史中任意添加一些东西

就像在场景 1 中，您忘记在提交中添加一些内容。但是，您不是将它添加到您的最后一次提交中，而是希望将该更改修改为历史上更早的一次提交。对于这种情况，交互式 rebase 为我们提供了编辑选项。
我们从交互式 rebase 命令(见上文)开始，并在我们想要更改的提交上设置 edit 命令:

```
pick bcfd87e add sql scripts for database
edit 9fb0b9c prevent users from changing their email
pick e0b46b9 cleanup web config 
```

Enter fullscreen mode Exit fullscreen mode

交互式重置基础现在在这个提交处停止，您可以添加您的更改:

```
Stopped at 9fb0b9c...  prevent users from changing their email
You can amend the commit now, with

  git commit --amend 

Once you are satisfied with your changes, run

  git rebase --continue 
```

Enter fullscreen mode Exit fullscreen mode

完成后，您只需将更改添加到暂存区，并使用 amend 命令提交它们:
`git commit --amend`
差不多完成了！现在我们要继续互动 rebase:
T1

## 场景 3:合并提交

我试着尽可能多的承诺。这意味着我在一个特性分支中的 git 历史通常是这样的:

```
9edf77a more review findings
67b5e01 review findings
940778d enable users to change their name
dc6b0db enable users to change their name
dfdd77d wip 
```

Enter fullscreen mode Exit fullscreen mode

这在特性开发期间可能是有用的，但不是对整个 git 存储库历史有用。因此，我想把所有的提交合并成一个。有两种方法可以做到这一点:挤压和修正。这两个命令都将一个提交合并到前一个中。唯一的区别是 squash 允许您提供一个选项来编写新的提交消息，而 fixup 则丢弃该消息。因此，让我们粉碎一些提交。我们像以前一样开始交互式重定基础，并设置适当的选项:

```
pick dfdd77d wip
squash dc6b0db enable users to change their name
squash 940778d enable users to change their name
squash 67b5e01 review findings
squash 9edf77a more review findings

# Rebase 63a2356..9edf77a onto 63a2356 (5 commands) 
```

Enter fullscreen mode Exit fullscreen mode

Git 现在提供了所有消息的概述:

```
# This is a combination of 5 commits.
# This is the 1st commit message:

wip

# This is the commit message #2:

enable users to change their name

# This is the commit message #3:

enable users to change their name

# This is the commit message #4:

review findings

# This is the commit message #5:

more review findings

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit. 
```

Enter fullscreen mode Exit fullscreen mode

我们删除所有的行，只写我们最终想要的消息:

```
enable users to change their name

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit. 
```

Enter fullscreen mode Exit fullscreen mode

瞧，我们已经把我们的提交压缩成一个:

```
cb88cf6 enable users to change their name 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用 fixup 命令做同样的事情。在下面的场景中，我对提交消息很满意，我想把我的提交合并到:

```
pick dc6b0db enable users to change their name
fixup dfdd77d wip
fixup 940778d enable users to change their name
fixup 67b5e01 review findings
fixup 9edf77a more review findings

# Rebase 63a2356..9edf77a onto 63a2356 (5 commands) 
```

Enter fullscreen mode Exit fullscreen mode

结果与上面的壁球示例相同:

```
b8e76d1 enable users to change their name 
```

Enter fullscreen mode Exit fullscreen mode

## 场景 4:拆分提交

有时，我们倾向于将两个或更多不同的话题合并成一个话题

```
7080968 add sql scripts for database
172db2b prevent user from changing their email and cleanup web config 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，将我们的 web 配置的清理分开会干净得多，不是吗？此时，我们可以再次利用编辑命令。让我们开始交互式的重新构建基础会话，并这样做:

```
pick 7080968 add sql scripts for database
edit 172db2b prevent user from changing their email and cleanup web config

# Rebase dfef724..172db2b onto dfef724 (2 commands) 
```

Enter fullscreen mode Exit fullscreen mode

如您所料，Git 在我们提交时停止。现在我们可以将我们的更改放回工作区:
`git reset HEAD~`
剩下的就简单了，只需为我们的更改创建两个提交:
`git add [files]`
`git commit -m "prevent user from changing their email"`
`git add [files]`
`git commit -m "cleanup web config"`
不要忘记继续 rebase 会话:
`git rebase --continue`
让我们看看结果:

```
9fb0b9c prevent user from changing their email
dfef724 cleanup web config
7080968 add sql scripts for database 
```

Enter fullscreen mode Exit fullscreen mode

整洁！

## 场景 5:重新排序提交

如果您想要合并两个或更多的提交，但是它们没有按顺序排列，该怎么办？

```
09f43c9 validate user inputs
62490ed import user data
c531f57 validate user inputs 
```

Enter fullscreen mode Exit fullscreen mode

您只需在 rebase 编辑器中对提交进行重新排序。之后，您可以使用修复或挤压选项来合并它们。

```
pick 62490ed import user data
pick c531f57 validate user inputs
fixup 09f43c9 validate user inputs

# Rebase 6c70ff2..09f43c9 onto 6c70ff2 (3 commands) 
```

Enter fullscreen mode Exit fullscreen mode

## 场景 6:更改提交消息

对特定的提交消息不满意？在 reword 命令的帮助下，你可以随时改变它:

```
reword c531f57 validate user inputs
pick 62490ed import user data

# Rebase 6c70ff2..09f43c9 onto 6c70ff2 (3 commands) 
```

Enter fullscreen mode Exit fullscreen mode

Git 将在提交时停止，您可以选择更改消息:

```
validate user inputs

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit. 
```

Enter fullscreen mode Exit fullscreen mode

## 场景 7:放弃提交

需要完全恢复特定的提交吗？对于这种情况，我们可以使用 drop 命令:

```
pick bcfd87e add sql scripts for database
drop 9fb0b9c prevent users from changing their email
pick e0b46b9 cleanup web config

# Rebase dfef724..e0b46b9 onto dfef724 (3 commands) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，至少，在这里你可以看到为什么保持你的 Git 历史干净是重要的。假设您将不同的特性放入一次提交中，或者将一个特性分成多次(无意义的)提交。恢复要复杂得多。

## 不顺心的时候

重写 Git 历史时，不要担心会破坏什么。
首先，你可以随时用`git rebase --abort`
中止交互式重设基础会话。该命令将停止重设基础会话，并恢复你已经做的所有更改。
其次，即使你已经完成了一次重置，但不知何故搞砸了，你也可以恢复它。Git(幸运的是)跟踪您执行的所有命令。你用`git reflog`
打开这个日志

```
e0b46b9 (HEAD -> feature/my2, feature/my3) HEAD@{34}: rebase -i (finish): returning to refs/heads/feature/my2
e0b46b9 (HEAD -> feature/my2, feature/my3) HEAD@{35}: commit: cleanup web config
9fb0b9c HEAD@{36}: commit: prevent users from changing their email
bcfd87e HEAD@{37}: reset: moving to HEAD~
3892bc7 HEAD@{38}: rebase -i: fast-forward
bcfd87e HEAD@{39}: rebase -i (start): checkout master
3892bc7 HEAD@{40}: rebase -i (finish): returning to refs/heads/feature/my2
3892bc7 HEAD@{41}: commit (amend): prevent user from changing their email and cleanup web config
39262b5 HEAD@{42}: rebase -i: fast-forward
bcfd87e HEAD@{43}: rebase -i (start): checkout master
39262b5 HEAD@{44}: rebase -i (abort): updating HEAD
39262b5 HEAD@{45}: rebase -i (abort): updating HEAD
bcfd87e HEAD@{46}: reset: moving to HEAD~
39262b5 HEAD@{47}: rebase -i: fast-forward
bcfd87e HEAD@{48}: rebase -i (start): checkout master
39262b5 HEAD@{49}: rebase -i (finish): returning to refs/heads/feature/my2
39262b5 HEAD@{50}: rebase -i (pick): prevent user from changing their email and cleanup web config
bcfd87e HEAD@{51}: commit (amend): add sql scripts for database
7080968 HEAD@{52}: rebase -i: fast-forward
dfef724 (master) HEAD@{53}: rebase -i (start): checkout master
172db2b (feature/my) HEAD@{54}: checkout: moving from feature/my to feature/my2
172db2b (feature/my) HEAD@{55}: commit: prevent user from changing their email and cleanup web config
7080968 HEAD@{56}: commit: add sql scripts for database
dfef724 (master) HEAD@{57}: checkout: moving from master to feature/my
dfef724 (master) HEAD@{58}: commit (initial): init 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以在开始交互式 rebase 会话之前搜索您的最后一个操作，并恢复到该状态。例如:
`git reset --hard HEAD@{18}`

所以，让我们战胜恐惧，让我们的 Git 历史保持干净！:-)