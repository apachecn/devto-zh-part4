# 你需要知道的最好的 Ruby on Rails IDE

> 原文：<https://dev.to/kevinhq/best-ruby-on-rails-ide-you-need-to-know-2gdf>

自 2012 年以来，我一直在与 Ruby on Rails 合作。在这七年的探索中，我偶然发现了许多 Ruby on Rails 的 IDE。我发现其中一些最适合我作为 Rails 开发人员的需求。因此，这对我的 Rails 开发伙伴来说也是最好的。

## 红药水

在我作为 Rails 开发人员的最初一年，当我还是一名本地 web 开发公司的内部开发人员时，我使用的是这个 RubyMine IDE。我记不太清楚了，但是公司里有人可以帮我们所有人拿到。对于初学者来说，使用这样的智能 IDE(就像它的标语一样)是非常有帮助的

可惜它的价格不便宜。对于个人使用，您只需为 IDE 每年支付 89 美元。然而，如果你运行的是开源项目，他们会免费提供。我不知道具体是怎样的，但是他们也给老师和学生，以及一个非盈利组织分发。

### 优点

对于初学者，我承认学习使用 Rails 很有帮助。它也像我们希望的大多数 IDE 一样智能(阅读:自动完成语法)。此外，它已经存在至少七年了，所以你不需要担心他们对未来支持和更新的承诺。

不仅如此，据我所知，RubyMine 是唯一专注于 Ruby on Rails 开发的 IDE。不像 Netbeans 或 Eclipse 等其他 IDE 主要关注 Java。

另一个优点是它支持所有三种主要的操作系统:Windows、Mac 和 Linux。

### 弊

虽然它是一个很好的 IDE，但是有许多弱点可能会阻止您使用它:

*   很贵的，个人执照。尤其是当您刚刚开始开发人员的职业生涯时。除非你有像我这样的赞助商免费给你，否则对新手来说可能是个经济负担。
*   尺寸很大。我记得 Mac 的安装文件有 200 多 MB。对于那些没有良好和稳定的互联网连接的人来说，下载它可能会令人沮丧。
*   因为它的尺寸很大，一旦你把它安装到你的笔记本电脑上，它会占用很大的存储空间。
*   与 Github 的 Atom 或 Linux 的 Textmate 或 GEdit 等较轻的文本编辑器相比，它更慢、更重。但是这是有意义的，因为它是 IDE，而不仅仅是一个文本编辑器。

早些年过去后，我开始转向更轻松的文本编辑器。几年后，我甚至用 Vim 来处理 Ruby on Rails，直到我发现 [Cloud IDE](https://kevinhq.com/category/cloud-ide/) 正在升级。在这一点上，我的客户的大部分项目出于几个原因要求切换到云 IDE。

但是，主要原因是进行代码审查是多么容易，对于一些较小的项目，这个云 IDE 甚至消除了设置临时服务器的需要。

## AWS Cloud9

以前是 Cloud9 (c9.io)，亚马逊收购后，他们将其与 AWS 基础设施集成。即使在他们迁移到 AWS Cloud9 之后，它的大多数功能和界面都与旧的 Cloud9 完全相同。

唯一的区别是你现在需要注册 AWS 账户才能使用这个 AWS Cloud9。定价也切换到“随用随付”模式，就像 AWS EC2 定价一样。

这是因为 AWS Cloud9 运行在 EC2 实例之上。如果你还不知道，他们将在本月底(2019 年 6 月 30 日)禁用你在旧 Cloud9 上的工作区。但是不要担心，您仍然可以下载并迁移您的工作空间到其他地方。

然而，最简单的迁移选择是迁移到 AWS Cloud9。我在这里写了我迁移它们的故事。迁移到 AWS Cloud9 后，我错过的唯一一个功能就是将预览 URL 设置为 public。

截至目前，要查看运行应用的预览网址，需要先登录 AWS 账号。在 old Cloud9 上，可以设置为 public。希望亚马逊早日恢复这个功能。

### 优点

它是云，运行在 AWS EC2 基础设施的顶层。从旧的 Cloud9 迁移到 AWS Cloud9 也很容易。亚马逊为你提供了详细的指南和演练。

因为是云，所以你可以在任何地方编写和调试代码。即使有一个大显示屏的安卓平板电脑，你也能做到。唯一的不便是你没有实体键盘。然而，如果你需要快速修复，而你仍然在外面，只是带着平板电脑而不是笔记本电脑，这是非常方便的。

另一个优点是它很容易设置运行 Ruby on Rails 应用程序。有了这样的云 IDE，你不用下载任何东西，只需打开你最喜欢的网络浏览器。

### 弊

迁移到 AWS 后，它的定价模式变成了“随用随付”。不仅如此，默认情况下，你的 IDE 将在 30 分钟不活动后关闭。亚马逊这样说是为了最小化成本。就个人而言，我更喜欢旧的 C9 定价，每月支付 19 美元，您的 IDE 将保持清醒。

## Repl.it

如果你不想把你的 Rails 应用程序迁移到 AWS Cloud9 中，有一个替代方法叫做 [Repl.it](https://kevinhq.com/repl-it-as-one-of-cloud9-ide-alternative/) 。它的价格与旧的 Cloud9 相似，但要便宜得多:每月 7 美元起。

优缺点与 AWS Cloud9 或多或少相似，只是其定价从 7 美元/月开始。即使相比老款 C9 19 美元/月，也还是半价。

## Gitpod

另一个可以成为 Rails 最佳 IDE 之一的云 IDE 是 Gitpod。与其他云 IDE 不同， [Gitpod](https://kevinhq.com/a-review-of-gitpod-is-it-worth-to-try/) 为你提供了与 Github 的无缝集成。

事实上，我相信如果您的项目被推送到 Github 库，Gitpod 将是最适合您的 Rails 开发的 IDE。唯一可能让你觉得不利的是他们的定价。

不限时数的等级起价为 39 美元/月。与另外两个相比，它要贵得多。但是，如果你不介意每月 100 小时的使用限制，你可以选择他们每月 9 美元的计划。

## 结论

您需要选择适合您大多数需求的 Ruby on Rails IDE。我们不能说 AWS Cloud9 或者 RubyMine 是其中最好的。许多人有不同的项目，有不同的需求。

例如，如果 Rails 项目很小，只有一两个开发人员，每月工作 100 小时就足够了，并且项目代码托管在 Github 上，那么 Gitpod 将更有意义。

或者，如果项目开发人员都在同一地点、同一时间一起工作，这意味着不需要如此花哨的云 IDE，那么 RubyMine 就可以派上用场。

否则，当你需要 Cloud IDE 却懒得注册 AWS 账号的时候，Repl.it 就是你最好的答案。然而，对于其基础设施依赖于 AWS EC2、使用 S3、数据库托管在 Amazon RDS 上的项目，AWS Cloud9 应该是最佳匹配。

最初发表于[你需要知道的最佳 Ruby on Rails IDE——Kevin 的技术博客](https://kevinhq.com/best-ruby-on-rails-ide-you-need-to-know/)