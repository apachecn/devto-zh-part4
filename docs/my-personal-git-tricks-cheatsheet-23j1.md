# 我的个人饭桶技巧小抄

> 原文：<https://dev.to/antjanus/my-personal-git-tricks-cheatsheet-23j1>

除了 Git 的“基本”命令，每个人都有自己使用的 Git 小技巧。我想快速写一份我自己的清单，我倾向于在我的`.gitconfig`中使用别名。滚动到底部可以看到一些有趣的在 git 之外运行的`git`相关命令！:)

## 快速修改

我经常忘记提交一个文件，或者留下一个。我绝对讨厌做像`removed console.log`这样的提交。因此，我添加了这个文件，就好像我要提交并运行:

```
git commit --amend --reuse-message HEAD 
```

Enter fullscreen mode Exit fullscreen mode

这将把文件添加到最后一次提交，并重用旧的提交消息。为了快速修复，我将这个别名为`git amend`

**注意**根据下面的反馈，也可以做`git commit --amend --no-edit`来达到同样的效果。

## 在原点/主顶点上重设基础

较老的分支经常落后很多，以至于我不得不加快速度来消除构建错误、ci 错误，或者仅仅是解决冲突。我最喜欢做以下事情:

```
git fetch origin # fetch latest origin
git rebase origin/master 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我将当前的分支提交堆叠在最新版本的 master 之上！

## 最后一次提交

有时候,`git log`变得势不可挡。由于我经常使用前面提到的`amend`命令，我倾向于只查看我的 git 日志中的最后一次提交:

```
git log -1 
```

Enter fullscreen mode Exit fullscreen mode

## 签出旧版本的文件(像一个锁文件！)

偶尔会搞砸一个和我分支无关的文件。大多数情况下，锁文件(mix.lock、package-lock.json 等)会发生这种情况。).我没有恢复一个可能包含一堆其他内容的提交，而是将文件“重置”回一个旧版本

```
git checkout hash-goes-here mix.lock 
```

Enter fullscreen mode Exit fullscreen mode

然后我就可以提交修复了！

## 精选

我偶尔使用的一个被低估的命令。当一个分支变得陈旧时，有时更容易从它那里得到真正需要的东西，而不是试图让整个分支达到最高速度。对我来说，一个很好的例子是包含不再需要的 UI/后端代码的分支。在这种情况下，我可能只想从分支
中挑选某些提交

```
git cherry-pick hash-goes-here 
```

Enter fullscreen mode Exit fullscreen mode

这将神奇地将提交带到您所在的分支。也可以做个清单！

```
git cherry-pick first-hash second-hash third-hash 
```

Enter fullscreen mode Exit fullscreen mode

你也可以做一个范围

```
git cherry-pick first-hash..last-hash 
```

Enter fullscreen mode Exit fullscreen mode

### 参考日志

这是一个超级用户特性，我很少使用它。我是说，一年一次！但知道这件事还是很好的。有时，我会丢失提交。我删除了一个分支，或者重置或修改了一个我不想搞砸的提交。

在这些情况下，知道`reflog`的存在是件好事。它不是您所在分支的单个提交的日志，而是您所有提交的日志——甚至是死分支上的提交。但是，日志会不时地被清空(修剪),以便只保留相关的信息。

```
git reflog 
```

Enter fullscreen mode Exit fullscreen mode

该命令返回一个日志，有用的是在提交的基础上进行挑选或重新设置。当你进入`grep`时非常强大。

## Bash 命令别名

除了 git 命令，我还喜欢使用一些有趣的 bash 别名来帮助我的工作流

### 当前分支

为了获得当前分支的名称，我使用了这个别名:

```
alias git-branch="git branch | sed -n -e 's/^\*  \(.*\)/\1/p'" 
```

Enter fullscreen mode Exit fullscreen mode

当我在另一个命令中运行`git-branch`或`$(git-branch)`时，我将获得我所在的当前分支的名称。

**注意**根据评论中的反馈，我把它换成了`git symbolic-ref --short HEAD`，它也能正常工作，但是你真的可以阅读它。

### 追踪上游分支

虽然我确信这在`.gitconfig`是可行的，但我还没想出怎么做。当我在一个新的分支上运行第一次推送时，我总是被要求为上游跟踪设置分支。这是我的别名:

```
alias git-up="git branch | sed -n -e 's/^\*  \(.*\)/\1/p' | xargs git push -u origin " 
```

Enter fullscreen mode Exit fullscreen mode

现在当我运行`git-up`时，我推送当前分支并设置上游跟踪！

## 反馈

基于评论中一些非常有用的反馈，我对我所使用的做了一些调整。

### 当前分支

看起来有很多新的方法可以获得当前的分支机构名称。如果向上滚动，您会看到我使用了一个疯狂的`sed`解析命令来获取分支名称。

以下是我的新选择:

```
alias git-branch="git symbolic-ref --short HEAD" 
```

Enter fullscreen mode Exit fullscreen mode

它似乎完全按照您的预期工作！