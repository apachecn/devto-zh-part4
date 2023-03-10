# 我用来加快工作速度的一些 git 别名

> 原文：<https://dev.to/kmehran1106/some-of-git-aliases-i-use-to-speed-up-my-work-1lbp>

大家好！我读过很多关于 git 的文章(大部分来自 dev.to ),其中很多人分享了 git 的技巧和诀窍。也有一些很酷的文章提到了版本控制的概念以及 git 实际上是如何做这些事情的。我真的很感谢这个美妙的开发者社区，它一直在成长！在这篇文章中，我想分享一些我使用的 git 别名，这可以帮助我避免重复的命令，也可以保持一个干净的 git 树。所以开始吧！

我已经将这个命令绑定到“lg ”,它以一种简单易读的单行格式显示了我的 git 提交历史。我忘了是从哪里拿的了，但不是从 stackoverflow 就是从这里！

```
log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit 
```

Enter fullscreen mode Exit fullscreen mode

我经常使用 rebase 来用 master 更新我的特征分支。这需要几个命令，我曾经在那里更新我的本地主分支，然后回到我的特征分支，重新设置更新的基础。可能有更好的或不同的方法来做到这一点，但我只是使用这一点，因为它的工作没有问题！如果任何人对此有任何建议，请随意评论！

`!git checkout master && git pull --rebase && git checkout - && git rebase --interactive master`

虽然这篇文章是我自己写的，但是我必须感谢这篇文章的作者，他以一种简单易懂的方式给出了 git rebase！
[Git Rebase 介绍我希望我有](https://dev.to/maxwell_dev/the-git-rebase-introduction-i-wish-id-had)

这是一个有时会派上用场的小命令！基本上，它恢复了我对 git 树(我想是:p)所做的最后一次更改，甚至可以恢复 rebases！

`reset --hard ORIG_HEAD`

有时我只需要用一些新的变化来更新我的最后一次提交。我用这个命令马上就能做到！

`commit --amend --no-edit`

Ez 提交！

`!git add -A && git commit -a`

好了，这篇文章就到这里吧！这些是我在日常工作中发现最有用的 git 别名。感谢开发社区中的每一个人(不管是不是 dev.to)每天给我的帮助(不仅仅是 git！)!