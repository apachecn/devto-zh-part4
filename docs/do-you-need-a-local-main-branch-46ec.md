# 你需要当地的主要分支机构吗？

> 原文：<https://dev.to/madhead/do-you-need-a-local-main-branch-46ec>

我最近注意到我(几乎)从未在 Git 中使用过本地的`main` / `master`分支。我只在我不打算支持的简单的一次性游乐场回购中使用它，并且从不在专业发展或“严肃”的宠物项目中使用它。

那么，真的需要吗？

我会说:在 Git 中有一个本地`main`分支几乎没有用。我已经在谷歌上找到了亚历克·本泽的这篇文章，我完全同意他所说的，但是让我把它放在我自己的帖子里。

很可能您的 Git 工作流禁止服务器上的`main`分支中的任何推送:它或者由 pull 请求更新，或者由具有特殊访问权限的团队成员(“看门人”)更新。所以，你很少把你的本地`main`推给它的远程对应物。此外，你需要让它与遥控器`main`保持同步，否则它会变得陈旧。没有利润的额外工作！

每次你需要一个`main`使用`origin/main`(假设你的遥控器是`origin`)，只是不要忘记`fetch`最新的遥控器状态。

例如，从 main 开始一个新的分支:

```
git checkout -b branch origin/main 
```

Enter fullscreen mode Exit fullscreen mode

检查任意分支与最新`main` :
的差异

```
git diff origin/main branch 
```

Enter fullscreen mode Exit fullscreen mode

根据最新的`main` :
调整您的分支

```
git rebase origin/main branch 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以签出到最新的 main，而无需真正签出任何分支:

```
git checkout origin/main 
```

Enter fullscreen mode Exit fullscreen mode

你会出现在一个分离的`HEAD`状态，并且不能提交。有些工具像 [`gradle-release`插件](https://github.com/researchgate/gradle-release)就不行了，但是，嘿！，您真的要从本地签出中释放吗？

感谢阅读到最后。愿你们的合并没有冲突！