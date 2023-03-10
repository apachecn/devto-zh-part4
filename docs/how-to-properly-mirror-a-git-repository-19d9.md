# 如何正确镜像 git 存储库

> 原文：<https://dev.to/sourcelevel/how-to-properly-mirror-a-git-repository-19d9>

当人们谈到镜像 git 存储库时，我们通常会想到一个简单的答案:

> 只要 git 克隆回购，你就可以了！！

然而，我们想要的镜像是复制原始存储库(或上游存储库)的状态。对于状态，我们指的是所有的分支(包括`master`)以及所有的标签。

当您将上游存储库迁移到一个新的“家”时，比如切换 GitHub 等服务时，您需要这样做。

与大多数工具一样，有很多方法可以实现这一点，但我将重点介绍其中的两种。区别在于您是否已经有了该存储库的工作副本。

## 镜像没有本地副本的 git 存储库

如果您以前没有克隆过存储库，您可以通过以下方式将其镜像到新的主目录

```
$ git clone --mirror git@example.com/upstream-repository.git
$ cd upstream-repository.git
$ git push --mirror git@example.com/new-location.git

```

这将获得上游存储库中可用的所有分支和标记，并将它们复制到新位置。

### 警告

不要在没有被`--mirror`克隆的存储库中使用`git push --mirror`。它将用您的本地引用(和您的本地分支)覆盖远程存储库。这不是我们想要的。阅读下一节，了解在这些情况下应该做什么。

另外，`git clone --mirror`比`git clone --bare`更受欢迎，因为前者也克隆了 git 注释和其他一些属性。

## 如果您已经有一个本地工作副本，镜像 git 存储库

通过工作副本，我们指的是一个“普通的”存储库，其中有被跟踪到 git 中的文件，您可以在这里执行像`git add`这样的命令。

在这种情况下，您可能有许多不想复制到新位置的本地分支和标记。但是你确实提到了远程分支。可以用`git branches -r`查看。但是，如果您注意这个列表，您可能会注意到上游存储库中已经删除了许多分支。为什么？

### 清除对远程分支的旧引用

默认情况下，当您执行`git fetch`或`git pull`时，git 不会删除对上游存储库中已删除的分支的引用(您可以在您的`.git/refs/remotes`目录中查看它们)。在将它们镜像到新位置之前，我们需要清理这些旧的引用。

为此，请运行

```
$ git fetch --prune

```

这将更新您对原始存储库的引用，并清除由`git branch -r`报告的陈旧分支。

### 最后，将存储库镜像到一个新位置

现在我们准备将这些更新的引用发送回`origin`存储库:

```
$ git push --prune git@example.com:/new-location.git +refs/remotes/origin/*:refs/heads/* +refs/tags/*:refs/tags/*

```

好吧，这里刚刚发生了什么？！

我们希望`.git/refs/remotes/origin`中的那些引用成为新位置的本地引用。那里的本地引用将存储在`refs/heads`目录中。同样的事情也发生在标签上。

`+`符号表示我们想要**覆盖**任何可能已经存在的引用。

`--prune`表示如果我们的`refs/remotes/origin/*`(和标签)引用中没有这样的引用，我们希望删除任何可能存在的引用。

## 结论

Git 当然不是一个容易学习的工具。虽然当你这样做的时候，它变成了一个非常强大和灵活的工具。

如果你想了解更多，请参见斯科特·沙孔写的优秀书籍。

你呢？有什么关于 git 的小技巧想分享吗？