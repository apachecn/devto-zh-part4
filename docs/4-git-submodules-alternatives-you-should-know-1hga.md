# 你应该知道的 4 个 Git 子模块

> 原文：<https://dev.to/giteden/4-git-submodules-alternatives-you-should-know-1hga>

### 想要开发更小的模块并在存储库之间共享？以下是一些有用的选项。

[![](img/3f75b228ca5ca9da3625bc5c91effdf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G4iKgXuA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A6T2ij2_Uxh-UnPngtenRmw.png)

有时，您需要将一个存储库中的代码放在另一个存储库中。这通常会产生一个问题，因为 Git 管理源代码而不是依赖项。因此，创建了一整套工具来在更模块化的层面上支持代码共享开发工作流，旨在弥合 Git Repo SCM 和其中的子回购之间的差距。

Git 子模块允许您将一个 Git 存储库作为另一个 Git 存储库的子目录。理论上，这允许您将另一个存储库克隆到您的项目中，并保持您的提交是独立的。为什么这很有用？因为它允许您从正在处理的项目中使用另一个项目。

然而，如果你在 Google 上快速搜索 Git 子模块，结果不会是肯定的。这是因为 git 子模块的一些主要缺点，例如被锁定到外部 repo 的特定版本，缺乏有效的合并管理，以及 Git 存储库本身并不真正知道它现在是一个多模块存储库的一般概念。

Git 的基础也不是用来处理组件之间的依赖和关系的。因此，围绕代码共享的工作流变得复杂，子模块正在努力交付我们想要的工作流。在 mercurial 中，子仓库被命名为要避免的最后手段的[特征。](https://www.mercurial-scm.org/wiki/Subrepository)

在本帖中，我们将建议和回顾 Git 子模块的有用替代方案，以帮助开发跨项目和在存储库中共享的多个组件。请随意尝试，并在评论中添加您自己的建议。

## [1。位(用于 JS)](https://bit.dev)

[https://www.youtube.com/embed/E5lgoz6-nfs](https://www.youtube.com/embed/E5lgoz6-nfs)

[Bit 是 Git 的一个开源扩展](https://github.com/teambit/bit),可以处理不同项目之间的源代码和依赖关系，以及粒度组件级别。

因此，它创建了一个工作流，用于在一个存储库中开发许多更小的组件/模块，同时能够在其他存储库中共享它们，并完全控制代码更改和依赖性。

您可以将 Bit 指向您的存储库中您希望在其他存储库中使用的文件和目录，它将开始跟踪它们的源代码，同时自动运行代码并定义所有依赖项。

然后，组件可以用版本标记并导出到远程托管集合(最好通过 [bit.dev](https://bit.dev) )，从那里它们可以导入到其他项目中——在那里您也可以开发它们并进行更改。

由于 Bit 跟踪代码并管理跨项目的依赖关系，您可以轻松地合并不同 repos 之间的代码更改，并更新跨整个代码库的依赖关系图。

结果是双重的:

1.  您可以轻松地管理一个存储库中的多个模块，而 Bit 处理它们之间的依赖关系。当你改变一个模块并更新它的版本时，你可以运行 bit status Bit，它会通知你这个模块被改变了，并询问你是否想更新依赖的模块。Bit 甚至允许您在同一个存储库中开发具有不同环境的模块，例如，在 JS 存储库中开发 ts。

2.  您可以在多个存储库中共享这些模块，而 Bit 将扩展 Git 的工作流来控制和同步它们之间的变化，并轻松地跨存储库更新您的整个依赖图。

如果使用 bit.dev，您还可以获得一个 UI，用于发现共享组件、协作甚至在线玩示例。[看一看](https://bit.dev)。

## 2。Git 子树

[![Source: [Atlassian blog](https://www.atlassian.com/blog/git/alternatives-to-git-submodule-git-subtree)](img/6170cb59a98ab9bfad9a0007d4f426ec.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--vS7qND5m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AqBgXG2ckpppn9fvJuMytGQ.png) *来源:[亚特兰蒂斯博客](https://www.atlassian.com/blog/git/alternatives-to-git-submodule-git-subtree)*

一个“git-submodule 命令的实验性替代方案”，它“将项目中的子树合并和拆分成子项目，然后再返回”。子树允许子项目包含在主项目的子目录中，可以选择包含子项目的整个历史。子树是一个子目录，可以以任何方式提交、分支和合并到您的项目中。子树合并的思想是，您有两个项目，其中一个项目映射到另一个项目的子目录，反之亦然。当您指定一个子树合并时，Git 可以判断出一个子树是另一个的子树，然后[适当地合并](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging)。

[与子模块](https://nering.dev/2016/git-submodules-vs-subtrees/)相反，子树的源文件存储在 repo 中。不仅仅是链接，代码真的在那里。此外，所需的步骤更少，对工作流程的更改也更少，因此许多人认为这个选项更可取。

## 3。饭桶奴隶

Gitslave 是一个“使用 Git 组合来自多个独立存储库的代码的大型项目的协同版本控制脚本”。它为 Git 提供了一个包装器，用于管理一个目录树，目录中有多个“从属”存储库。这意味着，您的 Git 命令行将能够从“主”存储库本身控制所有“从”存储库。

当在一个仓库中开发不同的子项目，并且需要合并工作流和它们之间的更新时，这可能是有用的。

包装器在每个存储库上运行 Git 命令，并组合所有存储库的输出。Gitslave 是一个额外的补充，除了超级项目中的一个新文件之外，还对。gitignore，也许还有一些私有的配置变量，不会影响你的库。

[gitslave](https://sourceforge.net/projects/gitslave/)

## 4。Lerna(用于多包 JS 回购)

[![](img/87905d61a814531faa363f65ca2e1c83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xqll7naf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AThKdRpHZcYp_Yau6DWUUFw.png)

“啊啊啊！Lerna 不管理源代码！它不是另一个回购中管理回购的工具！”—是的，你是对的。它不管理源代码。它不会整合您的存储库中的子项目的 Git 工作流。您将无法使用 Lerna 控制或合并对子项目的更改，也无法为项目中的子目录或不同存储库之间的子目录扩展任何其他源代码管理工作流。尽管如此，JS 开发人员可以使用 Lerna 来实现 Git 子模块的一些目标。

如果你的用例是正确的，Lerna 可以被证明是一个有用的方法来从一个单独的库开发和发布模块。您可以使用 Lerna 将您的存储库重构为一个多包存储库，在存储库中的不同目录中托管不同的模块，利用 Lerna 的特性来自动化模块的版本控制和发布，并完成工作。

每个模块都将使用一个独立包的所有配置进行设置，包括 package.json 文件和其他所有内容。因此，当您的最终目标是创建一个 JS 存储库，您可以在其中开发多个包并发布它们时，Lerna 作为 Git-Submodules 的替代方案就很方便了，因为它使这个工作流变得可行。

[上的“T1”](https://github.com/lerna/lerna)

### 优秀奖

[git-subrepo](https://github.com/ingydotnet/git-subrepo)

[git 背包](https://gerrit.googlesource.com/git-repo/)

在评论中补充更多…干杯🍺

## 相关岗位

[![Eden Ella](img/8cef84e53f0cc56054e03e934a0e23ed.png)](https://medium.com/bitsrc/building-a-react-design-system-for-adoption-and-scale-1d34538619d1) [## 构建一个适合采用和扩展的 React 设计系统|作者 Eden Ella | 2021 年 5 月|零碎

### 伊登艾拉<time datetime="2021-05-12T18:45:05.065Z">2021 年 5 月 12 日</time>T3】中](https://medium.com/bitsrc/building-a-react-design-system-for-adoption-and-scale-1d34538619d1)