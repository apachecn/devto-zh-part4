# 你们是如何处理数据库版本的？

> 原文：<https://dev.to/sushant12/how-you-guys-handle-database-versioning-f29>

在拥有大量开发人员和分支的大型应用程序上工作，可能会很快导致数据库被不必要的列所污染，这些列来自您从同事的分支或您自己的尚未合并到主数据库中的功能分支运行的迁移。

解决方案可能是回到你同事的分支，回滚该死的迁移，但我们只是人。

如果数据库可以随着
`git checkout -b <new branch>`改变就好了

并且用`git checkout <old branch>`记住老数据库

那么，你们用什么来解决这样的问题呢？

# 更新 1

目前，我正在编写一个 bash 脚本，它将为每个分支创建一个数据库。[https://github.com/sushant12/gla](https://github.com/sushant12/gla)

# 更新 2

所以，我刚刚写完剧本，它工作得很好。我将在工作中与我的同事一起测试我们的项目脚本。在那之前，你们可以自担风险尝试一下:[https://github.com/sushant12/gla](https://github.com/sushant12/gla)