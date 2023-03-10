# 恢复无法恢复的恢复的虚拟指南

> 原文：<https://dev.to/jeffpereira/a-dummy-s-guide-to-reverting-the-revert-that-could-not-be-reverted-1cp0>

昨天我遇到了一个有趣的问题，它源于几周前的一个情况。在服务架构上工作，并且在专门研究 React 的前端团队中，您有时会遇到一些棘手的情况。基本上有一个共同的努力来产生这个特定的功能，在最后一秒，产品所有者决定从发布中撤出它，因为后端代码中有一个 bug。很自然地，解决方案变成了，“如果没有 UI，客户就不能使用这个特性。”带着这个想法，我们继续前进，我提交了一份 PR，恢复了我为上述功能所做的 UI 工作。

现在让我们快进一两个星期，产品决定我们需要立即发布这个特性，因为 bug 已经修复。这是个好消息。我可以部署我的代码了！让我们来发现一些我尝试恢复我的更改的途径，以及我可以解决的问题。

为了讲好故事，让我们假设一些事情:

1.  我所有的工作都被压缩到一次提交中。
2.  我将所有工作保存在本地和远程分支上。
3.  在这种情况下，我没有遇到任何冲突，因为这是应用程序的一个新特性，没有其他人在开发。
4.  有时候，在大型团队中，当代码被挤在一起进行部署时，会发生非常愚蠢的事情。

* * *

嘿，笨蛋！只需恢复恢复，你很好！

在我的列表的最顶端是恢复恢复提交。剧透警报！这对我来说没有用，是一场疯狂的徒劳。原因如下。

这个解决方案的第一步是为恢复提交找到 SHA。出于某种原因，我无法在 Git 日志中找到它。它只是在已部署的标记分支的注释中。我很快意识到，在发布之前，恢复提交已经被压缩到一些其他的更改中，这造成了一个棘手的情况，我不能只是“恢复恢复我的更改的恢复”

* * *

嘿，笨蛋！只需调整一下，你就可以了！

```
git fetch
git rebase origin/develop 
```

Enter fullscreen mode Exit fullscreen mode

我尝试做的第二件事是在我们的 develop 分支上对我的旧特性进行简单的重新排序，但是没有产生任何有用的东西。如果我重定基础，恢复显然移除了特性中我的 UI 部分，我们仍然有问题。

* * *

嘿，笨蛋！只需更改提交 SHA，这样 Revert 就不能还原您的更改了！

在将我的本地分支重置到远程分支以便我可以撤销 rebase 更改之后，我尝试了这个方法。

```
git fetch
git commit --amend
// Write new commit message to change the SHA of my commit to trick the git revert into not reverting my changes

git rebase origin/develop 
```

Enter fullscreen mode Exit fullscreen mode

结果是一样的。revert 足够智能，知道我在做什么，如果我在重置基础后查看所有内容，我的更改会消失。

* * *

**解决方案#1:一次一个文件**

我们希望我的特性分支中的更改在开发中，对吗？因此，一种方法是检查我们的开发，创建一个新的功能分支，然后一个接一个地把东西带过来。

```
git checkout develop
git pull
git checkout -b (some_new_branch_name)
git checkout (branch_where feature is) -- src/.../...(path to file) 
```

Enter fullscreen mode Exit fullscreen mode

采用这种方法，我将不得不一个接一个地把每个文件带进来，希望在带东西过来的时候不要错过任何东西。我不喜欢这种方法，因为我的更改是在半打到十几个文件中进行的，如果我错过了其中一个文件，我会感到不舒服。这将意味着一个不完整的，甚至更糟的，被破坏的特性被交付到生产中。

* * *

我想我最后得到的是稍微更优雅的，一次就搞定了一切。

最后，我们有了一个分支，其中包含一些我们希望在 develop 中进行的更改，这些更改按照 Git lords 的意图被压缩到一个提交中。我真正想做的是保存那些更改，并将它们应用到我的开发分支。

这就是我所谓的优雅解决方案:

```
git checkout sexy_feature
git checkout -b backup_of_my_sexy_feature_just_in_case
git reset HEAD~1 
// The above will unstage and uncommit everything in the last commit.
git add .
// We need to stage everything since I added completely new untracked files
git stash
// This will put all of the staged changes into one stash
git checkout develop
git pull
git checkout -b bringing_sexy_feature_back
git stash pop
// Adds the changes into your branch, but not staged.
git add .
git commit 
// and you know the rest of the story from here 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，故事就平淡无奇了，我提交了一份 PR，在 Git 螺旋式下降中浪费了大量时间后，开始了我的一天。希望这篇文章能帮助一些遇到同样问题的人。