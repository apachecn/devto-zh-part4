# 你应该知道的 Azure Backup 的功能

> 原文：<https://dev.to/eddiesegal/features-of-azure-backup-you-should-know-about-7>

[![](img/643f17607918de870345a27bf61b53df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MtLtVOR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pixabay.com/photo/2014/05/27/23/32/matrix-356024_960_720.jpg) 

<figure>

<figcaption>图像由 Comfreak 从

<figure>[Pixabay](https://pixabay.com/illustrations/matrix-code-computer-pc-data-356024/)

<figcaption></figcaption>

</figure>

</figcaption>

</figure>

如果你生活在 21 世纪，你知道你需要备份你的工作。尽管如此，仍有许多公司没有适当的灾难恢复策略。严重的数据丢失对企业的影响是巨大的，[大多数遭受重大数据丢失的中小型企业最终都会倒闭。](https://consoltech.com/blog/10-common-causes-of-data-loss/)

这一切都始于糟糕的备份实践。最有效的备份系统包括使用云，这意味着通过第三方备份你的数据。虽然这减少了您对数据的一些控制，但好处远远大于缺点。

将您的数据备份到云是经济高效、安全且可扩展的。 [Azure Backup](https://cloud.netapp.com/blog/5-considerations-before-you-backup-on-azure) 是市场上最受欢迎的备份解决方案之一。在本文中，我们将探讨你可能应该知道的 Azure Backup 的五个特性，以便你可以充分利用他们的备份解决方案。

## 您应该探索的 Azure Backup 功能

### 补充方案

当谈到灾难恢复时，您希望确保您的所有基础都被覆盖。Azure backup 为你提供了两个[互补的备份解决方案](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-overview) : Azure Backup 和 Azure Site Recovery。它们的备份范围略有不同，提供了互补备份系统的组合。

**Azure Backup** 从您的虚拟机和本地服务器备份您的数据。这种备份系统最适合备份详细数据，而不是您的整个机器。例如，如果您出于合规性原因想要备份事务数据，那么 Azure Backup 将是合适的工具。

**另一方面，Azure Site Recovery** 也用于备份 VM 和本地服务器，但基本上将整个机器复制到另一个位置。如果发生灾难，您可以故障切换到辅助计算机，然后在辅助计算机再次运行时回切到原始计算机。

### 恢复您的整个机器或单个文件

Azure Backup 不会强制您恢复整个机器，如果您愿意，您可以选择简单地恢复单个文件。这为您提供了极大的灵活性，并且可以大大减少备份时间，因为并不总是需要备份整个机器。

### 冗余选项

Azure Backup 根据您的需求和预算，为您提供了四种不同的数据备份方式。

**本地冗余存储(LRS)** 是最便宜的数据备份方式。在此存储系统中，您的数据被复制三次，但存储在主区域中。这意味着，如果自然灾害(如洪水或火灾)袭击了数据中心，您的所有数据副本都将面临更高的风险。

**分区冗余存储(ZRS)** 是备份需要高可用性的数据的理想选择。此解决方案包括您的数据的三个副本，它们存储在主区域中，但位于不同的存储群集中。每个集群位于不同的可用性区域，并且在物理上与其他集群分开。

**地理冗余存储(GRS)** 比其他选项更昂贵，但也提供了最多的冗余。与 LRS 一样，使用 GRS，您的数据会在本地复制到主要区域。还向物理上远离第一数据中心的辅助区域制作第二拷贝。这是最安全的选择，因为它可以防范任何区域性自然灾害。这也是 Azure 备份的默认设置。

### 安全

您的数据是您最宝贵的资产之一，在当今世界，安全性是最受关注的问题之一。Azure Backup 提供不同级别的安全性。对于数据的传输和存储，Azure Backup 使用加密。只有你有加密密码(Azure 没有权限)，确保即使在 Azure 遭到破坏的极不可能的情况下，你的数据仍然是安全的。

Azure 需要身份验证才能进行高风险的更改，从而防止没有所需权限的用户进行与备份相关的更改。Azure 还会随时通知您发生了可能影响备份的异常更改。此外，如果前面的两种保护措施不够，任何被删除的数据都会被 Azure 存储 14 天。

### 多种方式备份

Azure Backup 提供了多种备份工作负载的方式，因此您可以用最方便的方式进行备份。第一种方式是通过 Azure 门户。你可以很容易地从你的浏览器访问它，并给你一个所有 Azure 服务的整合视图。

如果你精通技术，你也可以通过一个 shell 访问 Azure，比如 PowerShell AZ。如果您通过 shells 来指导备份，那么您可以使用 API 调用和脚本来管理和调度您的备份，为您提供更大的控制和自由。

## 结论

在当今快节奏的数字化环境中，我们的大部分工作都涉及某种类型的数据。为这些数据制定一个可靠的灾难恢复计划可以决定企业的成败。一次数据丢失灾难，如服务器崩溃，就能让一家公司崩溃。

将您的工作备份到公共云是保护您免受可能的数据丢失的最佳方式之一，而不必倾家荡产或在安全性上妥协。Azure Backup 提供了一个安全且用户友好的备份解决方案。为了充分利用 Azure Backup，请务必探索上面列出的功能。