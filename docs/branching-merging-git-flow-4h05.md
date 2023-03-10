# 分支和合并–Git 流

> 原文：<https://dev.to/jeastham1993/branching-merging-git-flow-4h05>

如果您错过了我关于分支、合并和 git 存储库的一般组织的系列文章的第一部分；你可以在这里找到[https://www . jameseastham . co . uk/source-control/branch-merging-part-1-should-you-do-it/](https://www.jameseastham.co.uk/source-control/branching-merging-part-1-should-you-do-it/)。

# Git 流程简介

我过去使用过分支和合并，但这是一个非常手工的过程。就存储库的管理而言，Git Flow 改变了我的生活。以前，我使用这样的命令:

 `git checkout -b my-new-branch
git add "mynewfile.txt"
git commit -m "Add mynewfile"
git checkout master
git merge my-new-branch` 

虽然这没问题，但有时确实有点乏味。

## 回车，GitFlow

GitFlow 是由 nvie 的 Vincent Driessen 首先提出的工作流设计。总的想法是，有一个中央“真相”回购。在大多数情况下，以及这篇文章的其余部分，这将是主。

从那里，开发人员将他们的变更拉到主分支。开发人员可能还需要从其他开发人员分支中提取变更(例如，如果多人正在处理同一个特性)。

一旦开发人员完成了一个特性的工作，它就被推回到主分支。

简单吧？

# 分支策略

何时以及如何创建分支的策略非常简单。有两个分支，具有无限的生命周期。

## 主人

主分支应该始终准备好投入生产。

始终保持主分支准确反映当前活动系统的状态

生产发布仅由主分支生成，这使得它与当前处于活动环境中的任何代码保持完美的一致。

如果使用连续交付管道，您可以在每次向 master 提交时触发生产发布。

## 养成

最简单的方法是将 develop 分支看作是为下一个版本准备的所有特性的集合。

任何将要添加的新特性都应该从开发中派生出来，而不是从主开发中派生出来。

这让我们很好地了解了三种类型的支持分支。

## 支撑树枝

除了两个核心分支之外，还有三种类型的支持分支。这些分支应该有一个有限的生命周期，这意味着一旦工作完成，这个分支应该被合并回一个核心分支并被删除。

## 特征

特征分支只能从开发中分支并合并回开发中。特征分支不应该直接与主分支相关。

可能有理由写一整篇关于分支命名约定的文章，但是我倾向于保持事情简单明了。类似于特征/特征描述。

我知道很多更大的组织也倾向于在分支中添加特定的开发人员姓名，给分支命名为 jeasthamdev/feature/description of feature。如果我在一个有多个开发人员的环境中工作，我会提倡这种做法。

## 热修复

修补程序是一个有趣的东西，因为大多数时候修补程序需要快速上线，而不是等待下一个发布周期。因此，修补程序分支总是从主服务器分支。

当一个修补程序完成时，它应该合并回主程序和开发程序中。这是非常重要的一步。合并到 develop 中可以确保在下一个版本中，修复程序不会被重新引入到系统中。

## 发布

发布分支最好被看作是开发人员和主人员之间的舞台。

当决定创建一个发布时，就会从 develop 中创建一个分支。这样就可以在发布前进行最后的修改、文档更新、版本编号和任何最终的错误修复。

通过在一个发布分支上完成所有这些工作，它立即清理了开发分支，用于新特性的工作。

一旦一个发布分支准备好被推向生产，它就被合并回开发和主控中。

这就是无聊的理论，现在让我们看看这是如何在日常基础上工作的。

# Git 流程——在实践中

在开始之前，Vincent 自己组装了一套优秀的工具，用于扩展标准 git 命令以支持 git 流。我将在本文的剩余部分使用它们，我也强烈建议安装它们。[https://github.com/nvie/gitflow](https://github.com/nvie/gitflow)。

为了让这篇文章尽可能专注于 git，你可以复制我在这里整理的示例库[https://github.com/jeastham1993/blog-release-flow](https://github.com/jeastham1993/blog-release-flow)。repo 包含一个极其简单且基本上无用的 node.js REST API，代码本身基本上与本文无关，但有一些实际的东西可以使用是很好的。

一旦本地有了回购，就该初始化 git-flow 工具了。你可以通过输入

 `git flow init` 

当您输入这个命令时，将会询问您一系列关于分支命名的问题，以及您将使用哪个分支作为主分支和开发分支。在大多数情况下，接受默认值是没问题的。

## 添加新功能

所以，我们想扩展我们无用的 REST API，以拥有另一个完全无用的特性，返回从 1-20 的数字列表。

 `git flow feature start numbersonetotwenty` 

运行上述命令将在本地创建一个名为 feature/numbersonetotwenty 的新分支，并将其签出作为活动分支。然后我们就可以出发，做出我们需要的改变。

如果您一直这样做，您可以将 server.js 文件编辑成如下所示

`
var express = require(" express ")；
var app = express()；

app.get("/branches "，(req，res，next) => {
res.json(["master "，" develop "，" features "，" hotfix "，" release "])；
})；

app.get("/numbers "，(req，res，next) => {
res.json([1，2，3，4，5，6，7，8，9，10，11，12，13，13，15，16，17，18，19，20])；
})；

app.listen(3000，()=> {
console.log("服务器运行在 3000 端口")；
})；

现在我们已经完成了对特性的处理，我们提交变更并完成特性

 `git stage server.js
git commit -m "[FEATURE] Add numbers one to twenty endpoint
git flow feature finish numbersonetotwenty` 

此命令会将您的更改合并回开发，删除特征分支并检出开发分支。

## 发布特征

我们现在希望将 numbersonetotwenty 特性投入生产，但是首先，我们需要更新我们的文档。为了创建一个发布分支，我们运行

 `git flow release start 1.1.0` 

我总是喜欢用产品发布版本的版本号来命名我的发布分支，但这是个人偏好。

一旦我们完成了任何版本变更，我们就可以运行了

 `git flow release finish 1.1.0` 

运行此命令时，将发生以下情况:

发布分支将被合并到主分支
主分支将用发布分支的名称标记
发布分支将被合并到开发
发布分支将被本地删除
如果您使用 CI/CD 管道，这将触发您的实时发布。

### 但是有一个 bug

眼尖的人可能已经发现了我在添加特性时引入的 bug，如果你没有，那么我将向你展示来自 GET /numbers 端点的响应，看看你是否能发现它。

 `[
1,
2,
3,
4,
5,
6,
7,
8,
9,
10,
11,
12,
13,
13,
15,
16,
17,
18,
19,
20
]` 

因此，让我们创建一个修补程序

 `git flow hotfix start fixnumbering` 

该命令将创建一个名为 hotfix/fixnumbering 的新分支，并将其签出。

然后，我们可以应用修补程序，也就是用下面的代码替换 server.js 中的第 9 行

 `res.json([1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]);` 

现在我确信我可以实际计数了，我可以提交更改并完成修复。

 `git stage server.js
git commit -m "[HOTFIX] Fix numbering"
git flow hotfix finish fixnumbering` 

运行“git flow hot fix finish fix numbering”会运行一组与完成发布分支非常相似的步骤，也就是说，它将:

热修复分支将被合并到主分支中
主分支将用热修复分支的名称标记
热修复分支将被合并到开发中
热修复分支将被本地删除

## 总结

这就是 Git 的详细流程。希望您可以看到完全接受高质量分支和合并策略的真正力量。使用特定于 git 流的工具使它变得更加容易，这实际上是一件很容易的事情。

如果你有使用 git flow 的经验，我很乐意在评论中听到你的成功和失败。

在本系列的下一篇文章中，我们将深入探讨微软的分支和合并模型——发布流程。