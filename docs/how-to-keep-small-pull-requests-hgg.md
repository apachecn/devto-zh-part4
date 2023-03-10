# 如何保持小的拉取请求

> 原文：<https://dev.to/kewah/how-to-keep-small-pull-requests-hgg>

*照片由 [@jdubs](https://unsplash.com/@jdubs) 在[上发布](https://unsplash.com/)T5】*

拉式请求(或 PRs)是团队协作的重要组成部分。它有助于发现问题，传播知识，并为我们的变革带来信心。当评审代码时，评审者也对将要合并的变更负责。所以，开公关的时候，尽可能多的分享信息很重要。它有助于评审者重建我们在工作时的心智模型(为什么我们要做这样的改变？我们是如何做到的？为什么我们要这样做？).关键是保持我们的公关规模小，重点突出。有几次提交的 PR 很好，这甚至是分享我们的想法和变化的绝佳方式。但是这些变化都与同一个目标有关。当 PRs 很重要时，就很难发现 bug、设计缺陷，评审者可能会感到不知所措。更糟糕的情况是批准变革而不关注它，这将违背与减贫战略合作的最初目的。

在这篇文章中，我们将通过不同的场景将一个 PR 分割成更小的块。

## 场景 1:干净的历史，但是太多不相关的变更

在打开我们的 PR 之前，我们意识到我们可以将一些提交移动到不同的 PR 中。例如，我们在这个过程中创建了一个新的助手功能，它将帮助评论者更加关注它。

```
$ git log
## branch: new-feature
* 8ee70d7 Commit 3
* b6a5da4 Add new helper
* d3b11d2 Commit 1 
```

我们将从`master`创建一个新的分支，并使用`cherry-pick`重用提交“添加新助手”。然后为新的分支机构创建一个 PR。

```
$ git log
## branch: new-feature

$ git checkout -b style-helper master
## branch: style-helper
$ git cherry-pick b6a5da4
$ git push origin 
```

我们现在可以在 GitHub 上打开拉取请求。

最后一步是将`new-feature`推送到 GitHub，但是当我们打开 pull 请求时，需要**改变基础分支**使用`style-helper`分支。(在 GitLab 上它被称为“目标分支”。)我们可以在 GitHub 接口上检查提交历史，看到“添加新助手”不在其中。合并`style-helper` PR 后，不要忘了在合并`new-feature`前把基地分公司改成`master`。

## 场景 2:需要拆分一些提交

这一次，我们在同一个提交中混合了不同的更改。我们仍然想为我们的样式助手创建一个不同的 PR，所以我们需要将这个提交分成多个提交。

```
$ git log
## branch: new-feature
* 8ee70d7 Commit 2
* d3b11d2 Commit 1 <-- commit we want to split 
```

第一步是使用`rebase`返回到“提交 1”，然后我们将使用`add --patch`对提交的`reset`进行分割。

```
## branch: new-feature

git rebase -i d3b11d2^ 
```

Vim，或者在 gitconfig 中定义的编辑器，将会打开，我们指定我们想要哪个提交`edit`。

```
pick 8ee70d7 Commit 2
e d3b11d2 Commit 1 
```

我们现在回到了“提交 1”阶段，现在是时候拆分它了。我们首先需要`reset`提交，将更改放回未暂存状态，这样我们以后就可以使用`add --patch`。

```
$ git reset HEAD~
$ git add --patch 
```

我们根据提示，使用“是”、“否”或“编辑”来决定我们想要展示的内容。

```
$ git commit -m "Split 1"
$ git add --patch
$ git commit -m "Split 2: style helper" 
```

我们现在已经完成了提交的拆分。我们可以完成`rebase`。

```
$ git rebase --continue
$ git log
## branch: new-feature
* 8ee70d7 Commit 2
* 02dbeb3 Split 2: style helper
* cf4ca41 Split 1 
```

我们处于与第一个场景相似的阶段，我们将通过相同的步骤打开两个 PRs。

## 场景三:一团糟，让我们改写历史

有时事情没有按计划进行，我们最终会有一个意大利面 Git 历史。在这种情况下，最好的办法是重写。

第一步是找到我们想要重新开始的提交。

```
$ git log
## branch: new-feature
* 255e8d5 Stuff 4
* 5c3fa7e Stuff 3
* deea3e8 Stuff 2
* 10a2b4a Stuff 1 <-- from here

$ git reset 10a2b4a^ 
```

现在一切都回到了不稳定的状态。我们将使用`add --patch`提交我们希望包含在第一个 PR 中的变更。然后`stash`剩下的创建一个新的分支，从那里继续提交，最后打开我们的第二个 PR。

```
## branch: new-feature

$ git add --patch
$ git commit -m "Commit 1"
$ git stash
$ git push origin 
```

我们可以使用`master`作为基本分支来打开第一个 PR。

接下来，我们为第二个 PR 创建一个新的分支。

```
## branch: new-feature

$ git checkout -b pr-2
## branch: pr-2
$ git stash pop
$ git add --patch
$ git commit -m "Commit 2"
$ git stash
$ git push origin 
```

我们可以使用`new-feature`作为基本分支来打开第二个 PR。

我们重复这些步骤，直到我们没有任何东西留在舞台上。

## 结论

经历这些步骤可能会感觉很麻烦，但我认为向评审人员表示同情并让他们处于良好状态是很重要的。

这些是我遇到的将一份公关分成小块的场景，请随意分享不同的做法。