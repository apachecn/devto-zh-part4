# 关于 Azure DevOps 应该知道的几点

> 原文：<https://dev.to/niloshimasrivastav/points-one-should-know-about-azure-devops-h4a>

我们大多数人都听说过 Azure DevOps，但不知道是否要使用它，这可能是因为你的解决方案使用了像 Java、Angular 或任何开源技术这样的技术，换句话说就是非微软技术。因此，如果你对是否在 Azure、GCP 或 AWS 之间选择 DevOps 功能有疑问，没有必要与 Azure DevOps 中的“Azure”术语混淆，因为 Azure DevOps 功能齐全，能够处理你的项目需求。

什么是 Azure DevOps

Azure DevOps 提供服务来支持团队规划工作、共享/协作代码以及构建和部署应用程序。它有两种不同的风格——基于云的服务和基于内部部署的服务器。一件重要的事情是理解我们现在在 Azure DevOps 中有两个部分——

1.  Azure DevOps 服务，以及
2.  Azure DevOps 服务器因此，我们用作 TFS 的 Team Foundation 服务器现在被称为 Azure DevOps 服务器，它用于内部部署，我们所使用的被称为 VSTS 的 Visual Studio Team Services 现在被称为 Azure DevOps 服务，这只是云，我们可以在 Azure DevOps 中充分利用云服务。

Azure DevOps 不需要 Azure 订阅

由于 Azure 是 Azure DevOps 的一部分，我们经常搞不清楚我们是否需要 Azure 订阅，我会说这取决于你的需求。因为如果你想在 Azure 上部署你的应用，是的，你需要 Azure 订阅，但 Azure DevOps 的功能可以在没有 Azure 订阅的情况下得到利用。因此，简而言之——你不需要 Azure 订阅就可以使用 Azure DevOps 获得/管理你的项目的全部功能。这是因为 Azure DevOps 还为您提供了管理内部项目的解决方案。

使用 Azure DevOps 时的语言障碍

对于非微软开发人员/团队来说，是否使用 Azure DevOps 有时会非常混乱，因为 Azure 这个术语。有时，我们简单地假设它只适用于微软技术栈。但简单的答案是否定的。使用 Azure DevOps 来使用/部署/管理您的(开源)项目没有语言或平台障碍。
正如 Azure DevOps 的 Jamie Cool 已经提到的那样—
“任何语言、任何平台、任何云”。
你只需要了解并充分利用 Azure DevOps 的全部功能。
Azure DevOps 与 AWS 或 GCP 合作

有理由认为 Azure DevOps 将与 AWS 或 GCP 一起工作，所以答案是肯定的，它将与 AWS 或 GCP 一起工作，如上所述——任何语言，任何平台，任何云。但是如果你在 Azure 上，公平地说，作为一个用户，你将获得 Azure DevOps 的完整和最好的用户体验。
不仅是这些云，如果您使用任何其他云来托管服务，您将能够在您的项目中利用 Azure DevOps。

Azure DevOps 的定价/成本

此处提供了定价的详细信息。正如你可以在链接中看到的，Azure DevOps 对团队规模(直到)5 是免费的，所以如果你有一个小团队，你可以选择 Azure DevOps。您可以从免费尝试和利用其功能开始，随着您的团队的成长，您可以选择为您需要的服务付费。
在云中使用自托管时，在 Team Foundation Server 中运行构建不收取额外费用，这包括在 2017 年和 2018 年 TFS 服务器许可证中，还包括一个免费并发作业以及团队中每个 VS 订阅者的额外作业。如果你希望有更多的能力，你可以支付每个并行工作。

可用于 Azure DevOps 部署的选项–

在使用 Azure 与 DevOps 合作时，人们可以非常灵活地部署他们的解决方案——我们有三种选择——内部部署、在云中自托管和通过 Azure 中的托管服务。每个类别中都有多个用于部署的代理。
Azure DevOps Server 之前被称为 Visual Studio Team Foundation Server。设置 Azure DevOps 最简单的方法是将所有东西放在一台服务器上。
对于云中的自托管，如果你想构建和部署 Windows、Azure 或其他 Visual Studio 解决方案，你需要有一个 Windows 代理，这些代理也可以构建你的 Java 和 Android 应用。基本上，自托管 CI/CD 是为执行/运行他们自己的构建/发布服务器的客户准备的。如果您不想运行自己的服务器，可以使用 Microsoft 提供的 CI/CD。
你可以免费下载 Azure DevOps 服务器。你可以利用一些特性，比如使用源代码控制来上传/共享代码，通过 Scrum/看板跟踪进度，来执行构建和发布，这是 DevOps CI/CD 的一部分，你可以使用 Azure Pipelines。

使用 Azure DevOps 时，从大量工具中进行选择

Azure DevOps 中提供了一组广泛的功能，您可以使用所有这些功能，或者选择您现有项目工作流所需的功能。微软在其 DevOps 保护伞下提供了以下功能—
Azure Repos——当然，TFS 支持也在其中，与此同时，您将获得云托管的私有 GIT repos，并且您可以与您的团队协作/共享它。
Azure 板——像 Scrum/Kanban 这样的板被提供来在你的项目中充分利用敏捷概念，并跟踪进度。
Azure Pipeline——一个完整的 CI/CD 管道，用于构建测试和部署任何语言、任何平台和任何云。
Azure 测试计划——你可以创建测试计划并与你的团队分享
Azure 工件——你可以与你的团队创建/分享包，比如 Maven/NuGet

由于许多组织也将他们的资源放在了其他一些 DevOps 工具上，微软也提供了对这些工具的支持，如——T0、詹金斯、厨师、T2、Terraform、Ansible、T4、VSTS

Azure DevOps 有很多东西需要考虑，你只需要亲自探索它，看看它能为你做什么。希望你能从这本书中学到一些新东西。快乐学习！