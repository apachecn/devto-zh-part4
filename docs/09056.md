# Git 工作流礼仪

> 原文：<https://dev.to/irmerk/git-workflow-etiquette-1kgj>

## 为干净的代码库组织仓库

git 的版本控制提供了强大的协作，无论是紧密结合的技术团队还是开源格式的分布式网络。尽管如此，还是要适当小心。Git 在软件开发领域广泛使用，可以管理不断发展的代码库。它的大部分用处来自于从现有项目中分支出新工作的能力，以便孤立地处理它，直到准备好集成到项目中。

高质量的软件由健壮、有弹性、安全和高性能的代码组成。这些属性可以通过维护高质量代码的基础和坚实的文档历史来实现。任何人都应该能够加入到这个过程中来，并且很容易地了解和跟踪项目的状态。这就是 git 和 GitHub 的用武之地。

*跳到下面的*:

→ [分支 vs 分叉](#branchFork)
→ [Rebase vs 合并](#rebaseMerge)
→ [CLI](#cli)
→ [提交(消息)](#commitMsg)
→ [拉取请求(和审查)](#prReview)
→ [GitHub 问题](#githubIssue)
→ [开源](#oss)

#### 接近

[![Accord Project Log](img/4d3c2d47e9db4deb71837d75db916010.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bcCx-IZx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vfvvmu7kmqvepby5mch2.png)

作为智能法律合同的开源项目 [Accord Project](https://www.accordproject.org/) 的贡献者，我的经历使我采纳并实现了 git 工作流和礼仪的标准，我认为这是彻底而有效的。

我将分享这一点，作为对他人有益的参考，也作为我自己未来的历史参考。很明显，我同时使用了 git 和 GitHub。透视的话，可以参考 GitHub 官方的工作流程建议书( [GitHub flow](https://guides.github.com/introduction/flow/) )。

我在 Accord 项目中的大部分工作是在 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 中完成的，其中混合了一些特定领域的语言，但是这里展示的原则应该适用于任何语言。

* * *

### 树枝+叉子:

#### 分支

**优点**

`+`所有项目工作集中
`+`便于协作
`+`单一远程处理

**缺点**

废弃的树枝很容易变得杂乱

#### 分叉

**优点**

`+`增加用户分支机构之间的隔离
`+`主储存库的清洁度

**缺点**

`—`跟踪分支机构的难度
`—`协作需要额外的步骤
`—`经验不足的 git 用户的可访问性较低

虽然这两种方法都有好处，但我的一般原则是分支一个开源项目，分支一个更小或孤立的团队。在开放源代码中，保持主存储库干净整洁的动机更大，而在分支上快速而肮脏的协作的机会更小。相反，一个技术团队将受益于一个带有可跟踪分支的中央存储库。无论哪种方式都需要严密的组织。

作为一个开源项目，Accord 项目遵循 fork 工作流模型。尽管如此，`main`分支应该保持同步([同步指南](https://help.github.com/en/articles/syncing-a-fork))，每个特性、错误修复、发布或代码更改应该发生在不同的分支中。这将在下一节的[中详细讨论。](#rebaseMerge)

命名一个新的分支将是保持问题、拉请求和 git 历史的一致跟踪系统的第一步。这里重要的因素是一致性。

`name/issue-tracker/short-description`

**`name`** :从姓名首字母到全名再到 GitHub 用户名

**`issue-tracker`** :参考 GitHub 或其他敏捷用户故事来源的问题

**`short-description`** :描述该分支主要目标的一到三个单词，用连字符分隔

**举例**:

`irmerk/i7/new-feature`

在单个特性上协作的情况下，为该特性维护一个单独的，`main`分支，并从它维护单独的分支。这可以遵循以前的命名约定:

`main/i14/routing-service // team branch`

`irmerk/i14/routing-service // my branch`

`someone/i14/routing-service // someone else's branch`

个人分支可以合并到`main`团队分支，然后通过[拉请求](#prReview)与整体`main`合并。合并分支后删除它们。

### Rebase + Squash

团队通常的做法是在合并到`main`之前，将长提交消息链压缩或浓缩成一个或几个提交。这在像我这样的人频繁提交从而导致 git 日志混乱的情况下非常有用。压缩用于维护一个可读的 git 日志。

在将一个特征分支合并到主(`main`)分支之前，它应该从最新的`main`开始重设基础。稍后讨论的[拉请求](#prReview)将会把这个分支的所有提交压缩成一个可构建的提交，并合并成`main`。重定基础本质上是将一个分支(`main`)移植到您当前的分支中，方法是在那个分支(`main`)上应用您所有的提交，然后用修改后的版本替换您的分支。这通过在您的本地 git 中重写 git 历史来让您到达一个分支(`main`)。

把它想象成把你的分支移到`main`的顶端，而不是从`main`的早期版本中分支出来。

不需要从一个分支中仔细调查单个提交，每个对`main`的合并提交应该包含一个特性或 bug 修复的所有代码。这使得调查过程更加容易。

虽然在重置基础之前挤压减少了冲突，因为冲突解决的步骤更少，但它确实改变了每个人在 GitHub 上记录的存储库历史，因此不是最准确的表示。压缩前的重置保持了 git 日志的整洁，并且在 GitHub 上记录之前不会改变历史。

#### 交互式 Rebase

`git rebase -i`在一些情况下非常有用，比如需要快速删除一个提交。如果您的团队有一个策略，其中任何特性提交都必须包含该特性的测试，那么将几个提交压缩成一个会很有帮助。这将涉及到`git rebase -i HEAD~n`,并用提交的数量替换`n`——在那些提交行上将`pick`替换为`squash`。

如果你的项目，比如 Accord 项目，需要一个[开发者原始证书](https://developercertificate.org/)的签署，你可能会发现自己需要快速地改变一系列提交的信息。与之前类似，在需要更改的提交上，将`pick`更改为`edit`,并简单地为每个提交添加`git commit --amend -s`和`git rebase --continue`。`git push -f`入枝为拉请求。**警告**:如果使用不当，用力会导致可怕的后果，如果不确定如何使用，请咨询他人。

一般来说，如果你没有大的，令人困惑的冲突，`-i`(交互式 rebase)将是过度的。

#### 改写历史

虽然用`git rebase`重写历史在某些情况下非常有用，但还是要小心。不要打断别人的历史，不要无意中犯下错误。**警告**:避免将他人正在工作的物体推到远处的树枝上。

#### 恐惧

我还是害怕`rebase`和`squash`。合并冲突可能更频繁，看起来也更困难。我有过由于不正确地调整基数而丢掉工作的经历。

然而，如果你经常提交正在进行的工作，重定基础的复杂性应该很少。起初，解决冲突和`git rebase --continue`会让你感到害怕，但是继续努力。将在这些冲突的情况下进行合并提交，但这是如何解决冲突的重要历史。如果你觉得使用`git rebase --abort`不顺利，你可以再试一次——这在`rebase`尝试之前也会恢复。

#### 流量

我对一般工作流程的建议是:

1.  确保您当前在`main` →如果在`fork`工作，获取:

```
git checkout main
git fetch --all --prune
git merge --ff-only upstream/main
git push origin main 
```

Enter fullscreen mode Exit fullscreen mode

→如果在`branch`中工作，拉动:

```
git checkout main
git pull origin main 
```

Enter fullscreen mode Exit fullscreen mode

1.  为您的功能或错误修复创建一个新分支

```
git checkout -b branchName 
```

Enter fullscreen mode Exit fullscreen mode

1.  根据需要尽可能多地进行更改。最终提交应该构建并通过测试。
2.  确保你的分支位于`main`的顶部(相对于从分支分出的分支)。这确保了评审者只需要很少的努力就可以通过快进`main`来整合你的工作

```
git rebase upstream/main 
```

Enter fullscreen mode Exit fullscreen mode

1.  如果您以前将代码推送到远程分支，则需要强制推。如果不是，省略-f 标记。

```
git push origin branchName -f 
```

Enter fullscreen mode Exit fullscreen mode

1.  从这个分叉的分支在 GitHub 中打开一个 pull 请求。一旦这个合并到`main`中，记得在本地清理掉你的分支

```
git branch -D branchName 
```

Enter fullscreen mode Exit fullscreen mode

#### 合并

作为一种非破坏性的操作，合并可能很好。但是，如果您正在处理的`main`非常活跃，git 日志可能会很快被合并操作创建的额外提交所污染。

虽然许多开发人员不喜欢改变基础，并在他们的变更上求助于合并，但是合并并不总是最好的选择。如果是本地合并而不是重基础，最起码要压扁一个 pull 请求合并到 GitHub 上的`main`。这允许对 git 日志的`main`中的提交消息进行更好的控制。

* * *

### CLI

命令行界面(CLI)是一个可以运行所有 git 命令的地方。Git 图形用户界面(GUI)如 [GitKraken](https://www.gitkraken.com/) 和 [Tower](https://www.git-tower.com/) 都很棒。我选择不使用它们，因为它们很贵，而且网上找到的绝大多数涉及 git 的解决方案都是针对 CLI 的。

此外，了解 git 的 CLI 方法可以让您轻松地导航 GUI，但反过来就不一定了。学习通过 CLI 与 git 和 GitHub 交互将会极大地利用您的时间，尤其是如果您从事开源项目的话。

#### 差异

一个好习惯是在做任何事情之前利用`git diff`。这允许您确保提交的代码是您所期望的，所有调试语句都被删除，并且没有垃圾代码。

#### 日志

日志揭示了存储库中发生的所有事情的历史。这个工具有许多以特定方式显示提交历史的选项。完整的日志包含提交散列、作者、日期和消息。我的首选日志记录方式是构建一个表示分支结构的 ASCII 图

```
git log --graph --decorate --pretty=oneline --abbrev-commit 
```

Enter fullscreen mode Exit fullscreen mode

#### 责备

一种检查谁在什么时候修改了文件的方法是`git blame`。如果你在 [VSCode](https://code.visualstudio.com/) 中编码，我强烈建议你查看 [GitLens](https://gitlens.amod.io/) ，这使得这种检查内联且极其高效。

* * *

### 提交

在提交中应该捕获单个逻辑更改。一个提交中的多个逻辑变化——在这种情况下，您可能会发现自己在一个提交消息中编写了*和*——是需要分成两个单独的提交的良好指示。

经常承诺——不要让自己在没有承诺的情况下走得太远。小的、增量的和自包含的提交在将来更容易跟踪或恢复。

虽然按逻辑顺序提交是最理想的，但我建议按你工作的顺序提交——这是你所做事情的时间顺序历史。

#### 消息

花一点时间来完成这个过程——提交消息不应该太匆忙。提交的描述应该被很好地记录下来，因此对于将来阅读这篇文章试图理解为什么要进行更改的人来说是非常宝贵的——即使他们几乎没有上下文。这种可访问性对于完整的 git 历史和工作流来说是一个至关重要的目标。

包括外部信息参考，如问题或拉式请求。任何对他人或未来的自己有帮助的事情，现在就应该推理出来。项目的长期成功依赖于代码和日志的可维护性。起初的争论会带来健康习惯的回报。

编写提交消息时，使用终端，而不是编辑器。从终端提交鼓励以增量方式描述变更的思维模式，以及保持提交的原子性——提交不需要一段解释。这将帮助您创建一个 pull 请求消息，其中应该捕获整体变更。稍后将详细介绍。

应该通过始终将`-m <msg>`标志包含到`git commit`中来捕获简洁一致的提交消息。

#### 格式化

一个格式正确的`git commit`主题行应该总是能够完成下面的句子:

> 如果应用，该提交将 **`your subject line here`**

`type(scope): subject — footer`

**类型**:

*   `feat` —一项新功能
*   `fix` —一个错误修复
*   `docs` —仅更改文档
*   `style` —对格式的更改(缺少分号等)。)
*   `refactor` —既不修复错误也不增加功能的代码更改
*   `test` —添加缺失的或纠正现有的测试
*   `chore` —改变建造过程或辅助工具，或维护

**范围**:

*   新代码或最佳描述的焦点，在哪里可以找到更改。

**主题**:

*   变更的命令性描述，保持在 50 个字符以内(不大写，没有句号)

**页脚**:

*   GitHub 问题参考 ID

示例

```
feat(utilities): include optional state for test utility - I22
    // implement a feature in src/utilities/test/sagaTest.js
test(sagas): initiate test for failed templates fetch - I22
    // tests run in and of src/sagas/*
chore(package): install saga testing library - I22
    // edits to dependencies in package.json
docs(README): add netlify badge and update - I27
    // alter documentation in the readme file 
```

Enter fullscreen mode Exit fullscreen mode

### 拉请求

拉取请求(PR)是共享信息的最佳方式之一。虽然问题描述了什么可能是错误的或者是一个特性，但是 PR 提供了一个媒介来说明代码库实际上发生了什么变化。此外，它对于同行评审和责任也是极好的，因为它鼓励高质量的提交。如果做得好，构建 PR 的提交将向那些将来审查或检查代码的人讲述整个故事。

PRs 应由包含价值的代码的完整补充组成。因为内部的提交遵循一种模式，所以标题应该是内部所有提交的扩展或总结。因此，在前面我谈到挤压时，git 日志将保留每次提交的模式，即使在它被整理之后。强调一下，**PR 标题应该遵循上述** 中[描述的提交消息格式。](#commitMsg)

作为一个 GitHub 工作流工具，PR 的内部结构没有标题重要，保持格式的一致性和效率。这使得无论有没有 GitHub，git 日志都可以保持高效。

与提交类似，PRs 应该很小。一个试图做多件事的 PR(发现自己在标题中写了*、*？)应该是分了吧。

#### 清理

如前所述，在创建 PR 之前重新调整基线是一个很好的整理习惯。花一点时间合并过程中创建的任何额外的提交，或者为了清晰起见重新措辞提交。公关中的每一次提交都应该直接面向公关标题的目标，甚至是相关的 GitHub 问题。

#### 格式化

```
<!--- Provide a formatted commit message describing this PR in the Title above -->
# Closes #<CORRESPONDING ISSUE NUMBER>
<!--- Provide an overall summary of the pull request -->

### Changes
<!--- More detailed and granular description of changes -->
<!--- These should likely be gathered from commit message summaries -->
- <ONE>
- <TWO>

### Flags
<!--- Provide context or concerns a reviewer should be aware of -->
- <ONE>
- <TWO>

### Screenshots or Video
<!--- Provide an easily accessible demonstration of the changes, if applicable -->

### Related Issues
- Issue #<NUMBER>
- Pull Request #<NUMBER>

### Author Checklist
- [ ] Ensure you provide a [DCO sign-off](https://github.com/probot/dco#how-it-works) for your commits using the `--signoff` option of git commit.
- [ ] Vital features and changes captured in unit and/or integration tests
- [ ] Extend the documentation, if necessary
- [ ] Merging to `main` from `fork:branchname`
- [ ] Manual accessibility test performed
 - [ ] Keyboard-only access, including forms
 - [ ] Contrast at least WCAG Level A
 - [ ] Appropriate labels, alt text, and instructions 
```

Enter fullscreen mode Exit fullscreen mode

GitHub PRs 正在降价
请记住:这种变化对解决问题有什么作用，可能会有什么副作用？为什么有必要？试着先发制人，让评论者提问，在你的信息中做到详尽。

#### 草稿

GitHub 为还没有准备好接受审查的公关提供了一个有用的选择。如果你想在 PR 中有一个真实的来源，让其他人在实际审查之前进行交互，或者甚至只是为了确保代码保存在 GitHub 上，而不仅仅是保存在你的本地机器上，打开一个 draft pull 请求。

#### 评论

为公关审查设定一个标准很重要，同样重要的是要彻底。评审者是 git 历史和代码质量的守护者。这一点怎么强调都不为过。现在看来显而易见的事情，在几个月或几年后肯定不会如此。不要因为要求改变或别人要求你改变而感到难过。最好将原始代码合并到`main`中，而不是急于完成某个特性。

在通过 PRs 的流程和不阻碍进一步的编辑或制作以及保持质量之间取得平衡。每个审查者都应该判断某个问题是否足以阻止 PR。然而，任何参与 PR 的人都不应该审查合并到`main`中的内容。在这些角色之间保持健康的分离。一般来说，PR 作者修复代码比评审人员参与更快。

* * *

### [T2](#github)GitHub

#### 问题

非常容易维护，问题应该被宽松地使用。任何问题、想法或 bug——无论是否重复——都应该成为提出问题的足够理由。这些是项目中对话的基础，所以插入观点并建立具体的讨论记录，以便搜索和链接。

说到这里，在打开一个问题之前搜索一个项目。虽然重复可以很容易地关闭，但这是对其他贡献者的一种礼貌。此外，要彻底，并提供尽可能多的背景。也许甚至花一点时间阅读项目的文档，他们可能有格式化问题的指导方针。

问题的特征请求格式示例:

```
<!--- Provide a general summary of the feature in the Title above -->
# Feature Request 🛍️
<!--- Provide an expanded summary of the feature -->

## Use Case
<!--- Tell us what feature we should support and what should happen -->

## Possible Solution
<!--- Not obligatory, but suggest an implementation -->

## Context
<!--- How has this issue affected you? What are you trying to accomplish? -->
<!--- Providing context helps us come up with a solution that is most useful in the real world -->

## Detailed Description
<!--- Provide a detailed description of the change or addition you are proposing --> 
```

Enter fullscreen mode Exit fullscreen mode

#### 噪音

注意你展示给别人阅读的内容。值得他们花时间吗？避免发布简短、仓促的回答或回应。花点时间让你的贡献对自己和他人都有价值。提供尽可能多的背景信息，同时仍然有所帮助。太少和太多的上下文都很糟糕。

如果已经过了相当长的时间，向审阅者发送一个温和的提示。很容易忘记一个活跃的公关。

#### 开源

我推荐这个[指南](https://opensource.guide/how-to-contribute/)，为开源做贡献。GitHub 提供了很棒的功能，应该被开发人员用来获得最好的体验。可以标记问题以方便导航，可以实施保护以防止直接提交给`main`，并且可能需要多个审查者。

本指南旨在提供一个培养健康、协作氛围的框架。所有这些都可以应用于任何开源或专有的技术团队环境。

* * *

### 结论

贡献你想看的作品。礼貌、尊重、彻底和效率都是我们欣赏的东西，所以用 git 培养和保持良好的习惯，合作将是最好的。

感知在开源中很重要，其他人如何看待 git 日志会有很大的不同。花时间用高效的提交和 git 礼仪来关心日志。对于一个公司和一个新员工来说也是如此。

如有任何问题或反馈，请随时联系我。