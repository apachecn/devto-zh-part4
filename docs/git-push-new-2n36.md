# Git 推新

> 原文：<https://dev.to/stephencweiss/git-push-new-2n36>

每次我在 Git 中剪下一个新的分支，准备提交一个 PR 进行审核时，都会得到一个恼人的错误，提醒我需要设置上游目的地。

```
stephen /Users/Stephen/_coding/app_name ➾ git push
fatal: The current branch feature/813/feature-description has no upstream branch.
To push the current branch and set the remote as upstream, use

  git push —set-upstream origin feature/813/feature-description 
```

从“手工工作是一个错误”中吸取教训，我决定这一次，我要找出如何修复它。

## 总结问题

在我想出解决方案之前，我需要确保我理解了这个问题。

当我告诉 git 将我的分支推送到远程时，它需要知道在哪里。如果它不存在，我需要告诉它在我的远程存储库中创建一个上游分支。

Git 友好地为此提供了命令，每次我得到它时，我都要复制和粘贴它。

作为一个讨厌把手从键盘上拿开的人，每一个实例都是对潜在优化的提醒。

## 创建解决方案

之前已经发现了 shell 别名和函数的强大功能，我知道我有工具可以让它像其他 git 命令一样简单。

我不知道的是如何把这些碎片拼在一起。在伪代码中，你可以想象这样一个解决方案:

```
getCurrentBranch() => {/* … */}

pushNewBranch() => {
  curBranch = getCurrentBranch
  git push --set-upstream origin ${curBranch}
} 
```

幸运的是，我最近碰到了*如何*找到我当前的分支:`git branch --show-current`(在 v2.22.0+中)或`git branch | grep \* | cut -d ‘ ‘ -f2`更低版本。

在这一点上，我可以将我的答案放入 git push 函数，或者使用一个子例程。我选择了后者，现在在我的`~/.zshrc`文件中有了一个新的别名:

```
alias gpnew='git push —-set-upstream origin $(git branch --show-current)’ 
```

这确实是一件很小的事情，但是通过消除一部分冗余，它让开发体验变得更加愉快。

## 脚注

*   [手动工作是 Bug | ACM 队列](https://queue.acm.org/detail.cfm?id=3197520)
*   [下一级外壳:别名和函数](https://www.stephencharlesweiss.com/2019-02-13/next-level-shell-aliases-and-functions/)