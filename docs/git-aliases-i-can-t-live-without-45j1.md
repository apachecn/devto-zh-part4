# 我不能没有的 Git 别名

> 原文：<https://dev.to/tswicegood/git-aliases-i-can-t-live-without-45j1>

在日常的 shell 使用中，我尽量避免使用别名。我教 Git 已经十多年了，我不想把`gup`翻译成`git fetch && git rebase`或者把`gcp`翻译成 git cherry-pick `。我已经安装了[完成](https://github.com/Bash-it/bash-it/blob/master/completion/available/git.completion.bash)并使用它，但至少这样我可以看到正在使用的命令，所以我保持肌肉记忆的新鲜。

这并不是说我不用*或任何*化名。我不断地在新机器上绊倒自己，因为我已经开始期望`git st`或`git di`被别名为`git status`或`git diff`(一些颠覆性的习惯很难改变)。有几个是我最近添加的，我发现自己一天要接触好几次。

一切都建立在`git delete-merged`之上。运行该命令的别名很简单(下面是完整的别名):

`git branch --merged | grep -v '^\*' | xargs git branch -d`

这将获取所有分支的列表，过滤掉当前分支，然后在结果列表上运行`git branch -d`。除此之外，我还添加了`pad`和`fad`别名来引用拉取或获取和删除。再加上`--prune`，我可以检查我的本地 Git 存储库和分支。

以下是我所有化名的要点: