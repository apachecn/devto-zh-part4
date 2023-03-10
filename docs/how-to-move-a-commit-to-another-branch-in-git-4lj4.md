# 如何将提交转移到 git 中的另一个分支

> 原文：<https://dev.to/projectpage/how-to-move-a-commit-to-another-branch-in-git-4lj4>

这是我今天遇到的一个小问题，当我第一次学习 git 的时候，这个问题很可怕，但是只要你在本地工作，这个问题实际上很容易解决。

我们都做过——有时你忘记了你还没有创建分支，或者更糟的是你在错误的特性分支上，你开始添加一些代码并提交。然后，您意识到您将代码提交到了错误的特性分支或直接提交到了 master，现在代码放在了错误的位置。

只要你没有把你的改变推到原点，这是很容易撤销的。假设我们已经在分支 feature-a 上提交了 a1b2c3d，但还没有创建 feature-b 分支。

首先，我们希望提交到正确的分支，所以让我们记下要移动的散列，并从 master:
开始

```
git checkout master 
```

可选地(如果我们还没有分支),我们创建一个新的分支特性-b 来放置并检查它:

```
git checkout -b feature-b 
```

然后，确保您在正确的分支 feature-b 上(如果您刚刚创建了它，这可能是不必要的)，并且将这个提交挑选到那个分支中，以便将那个提交添加到 feature-b:

```
git checkout feature-b
git cherry-pick a1b2c3d 
```

最后，让我们重置特性——返回到先前提交散列的分支(比如 z1b2c3d)。使用 git reset - hard 将从 feature-a 分支中删除所有引用变更的提交，以及所有变更本身，而将该提交留在 feature-b:

```
git checkout feature-a
git reset --hard z1b2c3d 
```

您也可以通过多次提交来实现这一点，只需挑选几次，然后重置为您想要保留的最后一次提交。如果您错误地提交到本地主机，过程是相同的——只需精选到一个分支，然后重置主机。

只有在没有将提交推送到原点的情况下才这样做。