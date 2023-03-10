# 在 Git 中使用多个工作树

> 原文：<https://dev.to/oliverjumpertz/using-multiple-working-trees-in-git-122d>

您是否发现自己第二次克隆了一个 git 存储库，这样您就可以从事不同的工作了？或者，您是否曾经不得不隐藏所有的更改，或者快速提交，以便将您的注意力转移到解决刚刚从产品中报告的 bug 上？

无论你是否已经发现自己处于这样的情况(那一天可能会到来)，知道你使用的工具提供了什么总是好的。

## Git 工作树

在初始化一个本地 git 存储库之后，只有一个工作树。由`$ git init`或通过克隆存储库初始化的那个。在每一个给定的时间，在一个本地存储库中，恰好有一个分支被检出。
那个分支还是可以随意换的，来来回回。但是如果您想一次签出两个分支，您可以第二次克隆存储库，到另一个目录中，从而再次下载所有内容。但是这感觉有点麻烦，可能有点太多了，不是吗？

### 添加另一个工作树

幸运的是，git 提供了添加多个(链接的)工作树的特性，不需要远程存储库的另一个克隆。

在本地 git 存储库中，下面的命令为现有的分支添加一个新的工作树:

```
$ git worktree add ../another-folder-for-your-new-working-tree branch-ref 
```

Enter fullscreen mode Exit fullscreen mode

之后，您就有了一个新的文件夹，您可以将编辑器或 IDE 指向该文件夹，并在现有的分支上工作。新的工作树实际上链接到您现有的本地存储库，因此总是同步的。这意味着几乎所有东西都在原始的本地存储库和创建的每个工作树之间共享，因此也节省了磁盘空间。

你也可以直接在一个新的工作树中签出一个新的分支:

```
$ git worktree add --track -b a-new/branch ../another-folder-for-your-new-worktree origin/branch-to-branch-away-from

### for example:
$ git worktree add --track -b bugfix/foo ../project-x-bugfix-foo origin/master 
```

Enter fullscreen mode Exit fullscreen mode

### 打扫卫生

如果你想清理你的工作树，你可以直接调用

```
$ git worktree remove ../folder-of-your-working-tree 
```

Enter fullscreen mode Exit fullscreen mode

这种工作树目录也可以手动删除。然而，驻留在实际存储库中的一些管理文件不会被删除。但是在本地存储库中使用以下命令:

```
$ git worktree prune 
```

Enter fullscreen mode Exit fullscreen mode

你仍然可以轻松摆脱那些现在不必要的管理文件。

## 结论

链接工作树是一个非常好的特性，可以让你的库的多个阶段、发布或者分支在任何时候都可以被检出。我一直使用它们来减少在分支之间切换所需的时间，有时还保留我的编辑器的第二个(甚至第三个)实例，这样我可以更容易地比较代码的各个部分。它们在进行代码评审时也很有帮助，因为要评审的分支可以很容易地被检查出来，从而支持评审过程。试一试吧，也许工作树可以帮助你提高工作效率！

## 更多信息

如果您想了解更多，请查看官方 git 文档!

## 编辑历史

[@joanis](https://dev.to/joanis) 正确地指出我把`--b --track`放错了顺序。`--track -b`是正确的顺序。在帖子中，它们现在被正确地排列了！