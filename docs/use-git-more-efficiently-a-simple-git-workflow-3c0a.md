# 更有效地使用 Git:一个简单的 Git 工作流

> 原文：<https://dev.to/negarjf/use-git-more-efficiently-a-simple-git-workflow-3c0a>

毫无疑问，Git 是每个开发人员的必备工具之一。尽管 Git 很有帮助，但是不遵循最佳实践将会使它完全无用。这就是为什么有不同的标准工作流程来充分利用 Git 及其出色的特性。在很长一段时间里，我最初的 Git 和 GitHub 是一个备份代码并让我们在线访问它们的工具；这并不是完全错误的，但是有太多关于他们的事情，直到我开始在 [YasnaTeam](https://yasna.team) 做开发人员时才知道。
在我们的团队中，大约有 10 名开发人员在同一个存储库上积极工作。有相同的习惯和所谓的文化是避免冲突和混乱的必要条件。每天超过 100 次提交，事情很快就会失控。为了避免这样的问题，我们实现了一个修改版本的 [Gitflow](https://datasift.github.io/gitflow/IntroducingGitFlow.html) 。

这篇文章也发表在媒体上。

[![Negar Jamalifard](img/9c4c08404da69597468fcfe3f2947a00.png)](https://medium.com/@negarjf/use-git-more-efficiently-a-simple-git-workflow-c4e650289ec8) [## 更有效地使用 Git:一个简单的 Git 工作流

### negar Jamalifard<time datetime="2019-06-08T10:24:00.218Z">2019 年 6 月 8 日</time>![Medium Logo](img/838343aca75ac583751bef1d890c44f6.png)中](https://medium.com/@negarjf/use-git-more-efficiently-a-simple-git-workflow-c4e650289ec8) 

* * *

# 工作原理

我们有另外一个名为`dev`的分支，而不是一个单独的`master`分支。这两个分支都是锁着的，没有人能直接推进去。
`master`仅供生产使用。‌After 测试新特性，仓库所有者在每次发布前更新`master`。
`dev`是默认的分支，所有的开发人员从这个分支创建一个分支并合并回这个分支。`dev`总是领先于 master，并且是唯一会合并到`master`中的分支，频繁。

# 命名分店

我们有一套命名分公司的模式。

```
[Module Name]-[Type]-[Detail]
// Example
users-fix-attach-roles-issue#765 
```

Enter fullscreen mode Exit fullscreen mode

## 模块名称

由于我们的代码库中有一个模块化的结构，与每个模块相关的更改应该提交到一个带有模块名的单独的分支上。如果您的项目中没有这样的结构，可以删除这一部分。

## 类型

根据任务的类型，开发人员应该在以下类型中进行选择:

*   `feature`
*   `enhance`
*   `cleanup`
*   `refactor`
*   `fix`
*   `hotfix`

类型`hotfix`用于主分支上的紧急修复，不会经常使用。

## 明细

详细信息是对分支机构将要交付的内容的简短描述。

这种分支规则迫使我们将每个任务分成有意义的块，并频繁地合并到 dev 中，并且总是有新的分支。这种方法的一个很大的副作用是最小化冲突。

## 提交消息

为了有一个干净的和描述性的提交列表，我们也有一个提交消息的命名规则(我喜欢规则！).

```
[[Module Name]] [Commit Message]
// Example
[users] add fetch users endpoint 
```

Enter fullscreen mode Exit fullscreen mode

在每个提交消息中包含模块名似乎是多余的，因为在我们的分支中已经包含了模块名，但事实是在将所有分支合并到`dev`(或`master`)中之后，查看提交历史，您无法说出每个提交属于哪个分支。因为现在他们都属于`dev`(或`master`)分支。为了容易地过滤所有提交，我们需要在提交消息中的某个地方有模块名。

提交消息本身应该在语法上完成句子“这个提交将…”。

# 拉取请求

## Title

拉取请求的标题会自动填入分行名称。如果分支机构名称过于笼统，则需要添加更多细节。

## 正文

正文应该包含关于拉请求的细节。在我们的工作流程中，拉取请求通常与现有问题相关。我们为每个 bug 或特性创建一个问题。所以我们链接到拉请求体中的相关问题。我们还使用项目符号重点介绍了拉动式销售中的变化。我们已经为我们的拉取请求创建了一个[模板，以简化流程。](https://help.github.com/en/articles/creating-a-pull-request-template-for-your-repository)

[![A Pull Request Template](img/4578beca50bc031b598600c163c67fba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nckb91hE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j0wf7ulw3i1ew1ni2f6l.png)

# 回顾

代码审查是我们日常工作中最重要的部分之一。一旦创建了一个拉请求，创建者应该选择两个对等体来基于一些预先指定的标准审查代码。
一旦拉取被审核者批准，创建者可以合并到开发中并删除分支。

* * *

我们已经使用这个工作流程一年多了，并根据我们的需求频繁更新。你在你的团队中使用一些其他的 git 工作流吗？让我知道他们怎么样。