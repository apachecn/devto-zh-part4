# 如何改变你公司的软件开发过程？

> 原文：<https://dev.to/peter279k/how-to-change-software-development-process-in-your-company-2a8k>

上周，我们的 Azure 云经理抱怨云服务的高成本。

我只是不明白为什么我们要在这些云服务上花费高成本。

我尝试使用 Azure 价格计算器来计算我们上周使用的价格。

我也遇到了惊人的情况。价格计算报告如下:

| 服务名称 | Azure Web 应用程序 | Azure MySQL 数据库 | Azure 容器注册表 | Azure VM1 | Azure VM2 | Azure VM2 |
| --- | --- | --- | --- | --- | --- | --- |
| 每周价格(美元) | $2.47 | $9.54 | $0.93 | $17.44 | $7.35 | $22.28 |

正如我们可以看到这个价格表，Azure 虚拟机的成本非常高。

我问自己:为什么我们的开发者在 Azure cloud 中创建许多 Azure 虚拟机？

现有的 Azure 云服务好用吗？

最后，我弄明白了为什么他们只创建 Azure 虚拟机。

原因大概是他们不知道什么是云服务，什么是云服务架构。

我们大部分的网络服务和应用都是针对年度项目的。

项目开发周期不是长期的，他们将在两三年后完成。他们将会保持这种状态。

我只使用 Azure web 应用程序和一个 Azure 虚拟机，所以我在 Azure 云平台上的成本很低。但其他人没有。

我们不必在不同的 Azure 虚拟机上部署这些基于 web 的服务，我们可以考虑使用 Azure web 应用程序轻松地将基于 web 的应用程序/服务部署为 SaaS(软件即服务)。

并且对于集成其他软件开发过程来说，它将是舒适的。比如 CI，CD，DevOps。

而且我们只能对不同的 Azure web 应用使用相同的数据库。建筑形象如下:

[![Alt Text](img/0639d93433a893520dbd3f281fc0f68e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aNXDcWms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7kagtr5frs3me3p32kld.png)

为了降低 Azure 云平台的成本，我认为是时候举办一次关于 Azure 云平台的内部培训了。

我认为它应该包括我们部门的每个开发人员以下部分。

*   云计算架构概述。
*   其他公司的案例研究，并介绍如何使用云平台。
*   我的目标是这个长期的内部培训课程/研讨会。
*   Azure web 应用程序的指南以及如何使用/部署 Azure web 应用程序。
*   如何使用、构建和运行 Docker 镜像和容器？
*   任何其他有用的 Azure 云服务介绍。

当我向部门经理提到这个内部培训建议时，他非常高兴，并感谢我的帮助，让其他开发人员有机会学习和如何正确使用 Azure 云服务。

我不确定开发人员在完成我的培训课程后会熟悉这些服务。

反正我觉得聊胜于无。