# 如何为临时组织选择 Salesforce 版本

> 原文：<https://dev.to/matheusgoncalves/how-to-select-the-salesforce-release-for-a-scratch-org-485l>

您知道在创建临时组织时可以指定版本(预览版或早期版本)吗？

这在针对预览版组织的未来特性测试您的代码时尤其有用，[但它甚至比这更重要](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_scratch_orgs_version_selection.htm):

在每个主要的 Salesforce 版本中，您有机会在您的临时组织和沙箱中提前访问该版本，以便在您的生产组织升级之前测试新的自定义和功能。这称为 Salesforce 预览，在即将发布的版本中创建的临时组织称为预览临时组织。

通过这种方式，你不仅可以在即将到来的版本中尝试新功能，以检查它们是否**符合你的需求**，而且通过在预览组织中运行你的应用程序，你可以**主动**检查你的代码是否状态良好，或者是否会有**突破性的变化**。

然后，您可以将任何未来的 bug 添加到您的 backlog 中，并相应地计划您的工作，减少意外。

通常，您会创建与开发中心版本相同的临时组织。但是，在一年三次的主要 Salesforce 版本过渡期间，**您可以根据您的开发中心版本选择 Salesforce 版本**、预览版或早期版本。

在此更改之前，您必须在即将到来的版本上创建一个试验开发中心，以创建预览临时组织。现在，您可以使用您现有的开发中心，包括您现有的临时组织活动和每日限额。

因为 previous 和 preview 都是相对的术语，您的开发中心组织版本在发布过渡期间决定了它们的相对重要性。以下是当您试图创建一个具有某个版本值的临时组织时会发生的情况。

| **开发中心版本** | **预览** | **先前的** |
| 开发中心已升级至最新版本 | 错误(开发中心已经是最新版本) | 以前的开发中心版本 |
| 开发中心仍在 GA 版本上 | 开发中心版本之后的版本(新发布的 Salesforce 版本) | 错误(开发中心在 GA 版本上；先前版本不可用) |

重要说明:如果您没有指定版本值，临时组织版本与开发中心组织版本相同。

### 为特定发布创建临时组织

您可以在临时组织定义文件中或直接在命令行上指定发布版本。您在命令行上发出的任何选项都会覆盖您在临时定义文件中定义的内容。

找出你的开发中心组织在哪个实例上:[status.salesforce.com](https://status.salesforce.com/)。
将发布选项添加到您的[临时组织定义文件](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_scratch_orgs_def_file_config_values.htm):