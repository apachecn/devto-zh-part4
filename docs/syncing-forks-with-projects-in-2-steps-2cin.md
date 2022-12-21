# 分两步同步 Github 分叉

> 原文：<https://dev.to/dochan/syncing-forks-with-projects-in-2-steps-2cin>

大多数时候，在分叉和处理一个项目之后，其他的拉请求被合并，这使得你的副本落后于项目的当前状态。这篇文章通过一个简单的指南带你了解如何同步你的 fork。

## 1。从原始项目 repo 添加远程

这是原始项目的远程 URL，您希望从该项目更新您的 fork。

```
git remote add upstream git://github.com/ORIGINAL-DEV-USERNAME/REPO-YOU-FORKED-FROM.git 
```

现在检查是否添加了上游

```
git remote -v 
```

这将列出远程 url 在本地存储库中的活动状态

## 2。从原始回购更新分叉

运行以下命令进行提取。

```
git pull upstream master 
```

这将从原始回购中提取变更，并将其合并到您的本地副本中。这之后是快乐编码:)