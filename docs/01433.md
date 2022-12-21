# 给假人藏东西

> 原文：<https://dev.to/tohidheshmati/dirty-temporary-commit-369m>

git stash for dummy 或 dirty & temporary commit

我坚信通过简单的步骤来学习事物的核心，然后通过探索选项来拓宽知识面。

我昨天遇到了“git stash ”,下面是简单的步骤。

场景:我已经完成了一项任务，在任务进行到一半时，我读到了团队时差

`the develop branch has been updated. please update your branches`

但是我还在工作当中。我在自己的分支上写了几行代码。我不想提交或删除我的代码。删除是浪费时间。如果我真的提交了，那将是一个糟糕的提交，因为我对自己的工作没有把握。但是我想更新我的开发分支。我该怎么办？

方法是藏起来。通过存储，我们保存了我们的脏工作，并很容易切换到其他分支，更新它们，并回到我们的工作。

让我们称之为脏的和临时的提交。

首先要做的是做这个肮脏的承诺。这是命令，而不是普通的 git 提交消息。

```
git stash 
```

Enter fullscreen mode Exit fullscreen mode

要获得类似提交消息的消息，我们可以选择如下的保存选项。

```
git stash save "message" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以自由地去其他分支机构，做任何种类的更新。在我的例子中:检查开发分支+从原点提取+合并到我的任务分支:

```
git checkout develop

git pull origin develop

git checkout my_Task_Branch_Name

git merge develop 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的项目是最新的。但是我们想念我们写的代码。看到所有隐藏的(或我们的小秘密；)临时提交)

```
git stash list 
```

Enter fullscreen mode Exit fullscreen mode

你会看到一个或多个隐藏。强烈建议不要有一个以上的贮藏。无论如何，你需要找回你的代码，要找回你隐藏的代码，你必须使用这个命令来应用它。

```
git stash apply stash@{number} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，您已经完成了。恭喜你。你既更新了，又拿回了你的代码。如果清单困扰着你，就像我困扰着我一样，试试 pop 选项来应用和删除。

```
git stash pop stash@{number} 
```

Enter fullscreen mode Exit fullscreen mode

或者全部清除

```
git stash clear 
```

Enter fullscreen mode Exit fullscreen mode

走吧

同样值得注意的是，通过使用`drop`选项，您可以删除特定的或所有的 git 隐藏代码。有人可能会说`pop`选项好得多，因为它同时适用和下降。所以`pop`在某种程度上是这两者的结合。它可以是好的，但我个人不喜欢它。这可能会给你带来麻烦。至少开始时单独使用`apply` & `drop`是个好主意。

你也可能经常遇到~WIP~并且知道它代表~工作进行中~是件好事。

要查看更多选项和可能性，请点击[这里](https://git-scm.com/docs/git-stash)