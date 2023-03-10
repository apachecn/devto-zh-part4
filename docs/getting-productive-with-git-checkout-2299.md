# 使用 git checkout 提高工作效率

> 原文：<https://dev.to/dzhavat/getting-productive-with-git-checkout-2299>

这篇文章最初发表在我的博客上。

* * *

作为一名开发人员，我每天都使用 [git](https://git-scm.com/) 。它已经成为我工作中必不可少的工具。很棒，我喜欢。

我也喜欢直接在终端中与它交互。写一个命令并看到它做一些事情是很神奇的。我尝试使用 GitHub 桌面已经有一段时间了，但是总是回到终端。

### 问题

不过，有一个命令，我曾经既喜欢又讨厌。是`git checkout`。

我喜欢它是因为它简单有效。有了它，我可以创建一个新的分支，并立即切换到它。就像这样:

```
git checkout -b new-feature 
```

Enter fullscreen mode Exit fullscreen mode

太美了。

但是，在某些情况下，我也讨厌它，因为它的工作方式。如上所述使用它会创建一个基于我当前所在的分支的分支，这并不是我一直想要的。

这是我在工作中可能遇到的一个用例。

假设我在一个`new-feature`分支上，有一些变化，与当前任务无关，我想转移到一个新的分支。简单地使用`git checkout -b new-awesome-feature`对我没有帮助，因为到目前为止所有的提交都将随之而来。在这些场景中，我的工作流程是:

1.  藏起所有东西。
2.  切换到我想作为新的基础的分支。
3.  使用`git checkout`命令创建一个新分支并切换到该分支。
4.  从隐藏中弹出更改，并继续使用它们。

在如下所示的代码中:

```
git stash
git checkout master
git checkout -b new-awesome-feature
git stash pop 
```

Enter fullscreen mode Exit fullscreen mode

肯定不是最好的工作流程。

### 解

大约一个月前，我决定看看是否可以简化这个过程，因为我对它目前的形式不太满意。于是我打开 [`git checkout`](https://git-scm.com/docs/git-checkout) 文档，开始钻研。幸运的是，解决方案就在那里！在该页面的众多选项中，有一个叫做 [`<start_point>`](https://git-scm.com/docs/git-checkout#Documentation/git-checkout.txt-ltstartpointgt) 。通过使用它，我可以为新的分支指定一个起点。它说我可以使用“*提交名称*”，但实际上还有更多。跟随到 [`git branch`](https://git-scm.com/docs/git-branch) 的链接揭示了“ [`<start-point>`](https://git-scm.com/docs/git-branch#Documentation/git-branch.txt-ltstart-pointgt) ] ***可以作为分支名称、提交 id 或者标签*** ”给出。这正是我要找的东西！

所以现在，每当我想把变更转移到一个新的分支，这个分支必须有一个不同于当前分支的起点，我就使用:

```
git checkout -b <branch-name> <start-point> 
```

Enter fullscreen mode Exit fullscreen mode

上面的四个步骤被简化为一步！纯粹享受！

自从我发现了这个，我就和`checkout`命令和平相处了。我更喜欢它。希望你也一样！