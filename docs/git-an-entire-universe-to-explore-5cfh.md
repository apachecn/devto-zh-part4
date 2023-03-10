# Git，探索整个宇宙

> 原文：<https://dev.to/gattalraouf/git-an-entire-universe-to-explore-5cfh>

> 宇宙中充满了神奇的事物，耐心地等待着我们变得更聪明。

在这篇文章中，我们将探索 Git 世界，经历它的一些神奇的事情&理解它的语言。

## 什么是 Git？

> 宇宙是所有的空间和时间以及它们的内容。

谈到 Git，我们可以这样定义它:“它是一个版本控制系统”，这太专业了，换句话说，我们可以说它是所有版本、时间和它们的内容。版本控制系统(又名:VCS)是一种“数据库”。它可以让您随时存储版本(整个项目的快照)。当您稍后查看旧版本时，您的 VCS 会向您展示它与以前的版本有什么不同。它独立于您正在处理的项目/技术/框架的类型:

*   它对 HTML 网站和设计项目或 iPhone 应用程序一样有效
*   它可以让你用任何你喜欢的工具工作；它不关心你使用什么样的文本编辑器、图形程序、文件管理器或其他工具
*   它记录您对项目文件(内容)所做的更改。这就是版本控制的意义所在。

[![](img/ac219d25bd00a30082a1bbfd32620283.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sB_xhOIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/erynecwxpkg96phcwg0p.png)

## Git 的用途是什么？

我想说宇宙是用来被发现的，但这在这里并不合适，所以我更愿意在下面继续介绍使用 Git 为您的项目带来的诸多好处:

*   确保团队成员之间的良好协作，让他们在没有冲突的情况下，根据需要自由地修改项目。
*   允许在简单而正确地进行更改后保存项目的版本。
*   确保能够有效地恢复文件的旧版本(甚至整个项目)。
*   帮助您理解您的项目是如何在不同版本之间演变的。使用像 Git 这样的分布式 VCS 的一个副作用是它可以作为备份。

[![](img/7e497d543497525ead0b36038e1c452d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ORCbQUpQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0x8sqwwsmwbwxmz9zu6a.png)

## Git 的宇宙规则！！

> 掌握 git 的唯一方法是理解它是如何工作的，到处都写着命令！！

事实上，理解宇宙的语言是理解它的规则和发现它的秘密的唯一途径。git 也是如此，其他任何东西也是如此，为了理解 git 语言，我们应该通过回答以下问题来理解一些概念:

[![](img/9069ed3e0011275b86908170cfbb00d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jMPlzaWt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7k82ohii0qeudsvet5j4.png)

### 什么是工作目录？

它只是您计划进行项目的目录。

### 什么是提交？

提交是项目当前**阶段**(添加到阶段区域)变更的快照。你可以说它是该项目的安全版本。

### 什么是集结地？

这是准备提交的**，你可以说这是我们收集要拍摄的项目变更的场景。**

 **### 什么是储存库？

答案是，git 存储项目文件变更的数据结构，但这太专业太正式了，为了简单起见，我们称之为:**存储项目文件快照的“容器**。

*   除了提交之外，它还存储了其他东西，比如配置文件和一组提交引用，称为 **heads** (我们将在另一篇文章中讨论)。
*   它存储在与项目本身相同的目录(工作目录)中，作为一个名为“的隐藏子目录。饭桶”。
*   有两种类型的存储库，本地的是保存在你电脑上的副本，远程的是保存在中央服务器或任何代码托管服务中，比如 GitHub。

### 我们说的 git 中的 Hash 是什么意思？

git 中的散列是由加密算法(SHA-1)从任意长度的字节序列构建的简短摘要。在英语中，它是标识整个文件唯一的短代码。

## Git 的宇宙故事？

通过将我们刚刚理解的概念放在下面的工作流中，理解 Git 世界的秘密将会非常容易:

[![](img/35d805625465ba45d1e5801d01f58d00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h_x7_Npj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/huxn761odzxda90ilosa.png)

### A .初始化项目

要初始化项目，有两种可能的方案:

1.  使用 **git init** 命令开始一个全新的项目。
2.  使用 **git clone** 命令从远程存储库中克隆一个项目。

在这两种情况下，您的工作目录将包含一个名为**的隐藏子文件夹。git** 包含 git 工作所需的所有信息。

[![](img/1d30faf73ae0893f451c5325a6970ebd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ghA5sXEE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/icclb3w0wcsne41ap58i.png)

### B .构建一个完整的特征

代码、代码和更多代码…

[![](img/add75bf65d8377fa49236a8d1c2f44ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5UH7YZVp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r18y85bl9m38rgoj0yyt.png)

### C .筹备你的工作

使用 **git add** 命令，使您的更改在 git 中可追踪。暂存工作是您正在构建的要提交的功能的准备步骤。

[![](img/7c59ba85e5fa05a9ddcdadd2c83af3e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aD30DcrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j9aaru63j8yjhmytt3ox.png)

### D .提交更改

在完成所有更改后，剩下的就是使用 **git commit** 命令来压缩它并将其存储到自己的数据结构中。压缩后的对象将有一个唯一的名字，一个散列，并将被存储在**中的**对象目录**下。git 文件夹**。

事实上，当您提交时，git 只做两件事来创建工作目录的快照:

1.  如果文件没有改变，git 只是将压缩文件的名称(散列)添加到快照中。
2.  如果文件发生了变化，git 会对其进行压缩，并将压缩文件存储在 object 文件夹中。最后，它将这个压缩文件的名称(散列)添加到快照中。

简而言之，我们应该理解提交由 4 件事情组成:

1.  工作目录快照的名称(哈希)
2.  一个评论
3.  通勤信息
4.  父提交的哈希

[![](img/7ba8191432a09345a1ded85f3b60d791.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dD0TO2-c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ow1r6hgo2dp83z8x6vb9.png)

### E .构建另一个特征并重做之前的…

> 每次提交时保存的数据是 git 跟踪每个文件的状态、了解提交之间的差异并保存更改的方式，更准确地说，git 使用提交及其父级的散列来做到这一点。

## 待续…

> 宇宙的秘密永远不会结束…

我们刚刚介绍了为了轻松使用 git 您应该了解的基本工作流程。然而，你应该知道这样做的正确命令，除了在[这里](https://github.com/Gattalraouf/Git-GitHub-Valuable-Resources)的一些有价值的资源之外，你还可以找到细节(主要在备忘单中)。
其他方面如分支将在其他文章中详细说明。**