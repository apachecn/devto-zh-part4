# 伟大的 Azure DevOps 迁移-第 4 部分:验证

> 原文：<https://dev.to/timothymcgrath/the-great-azure-devops-migration-part-4-validation-kh8>

我们已经设置了临时服务器。我们已经清除了不想导入的数据。我们差不多准备好了！

我们需要在本地服务器上运行 Azure DevOps 服务验证，以验证在导入之前没有问题。该验证将提醒我们在实际导入之前需要解决的任何问题。

如果你错过了之前的帖子，从这里开始。

我强烈推荐[微软的 Azure DevOps 服务迁移指南](https://azure.microsoft.com/en-us/services/devops/migrate/)。

### 获取工具

从下载[数据迁移工具](https://azure.microsoft.com/en-us/services/devops/migrate/)开始。该工具包含您一定要阅读的指南和实际的迁移工具。

复制。将数据迁移工具的 zip 文件解压到临时服务器，并将其解压到 C:。

### 运行验证

打开命令提示符，将目录更改为解压缩后的 DataMigrationTool 文件夹。这个文件夹包含 Migrator.exe 的文件。

要执行验证，请从命令提示符运行迁移器验证/收集:[收集名称]。确保您在您的临时服务器上执行此操作，使用 localhost:8080 确保您指向正确的服务器。

验证只需几分钟即可运行，并且会创建许多包含结果的日志文件。

### 分析结果

您可以在命令提示符下或存储在 DataMigrationTool 的 Logs 文件夹中的日志文件中查看验证结果。打开日志，选择您验证的集合，然后单击最新的文件夹(为每个迁移验证创建一个文件夹)，然后打开 DataMigrationTool.log。

我有一些问题需要解决，我将在下面解释。您可能会得到不同的版本，您可以在[迁移故障排除指南](https://docs.microsoft.com/en-us/azure/devops/migrate/migration-troubleshooting?view=azure-devops)中查找。我遇到的问题没有一个是特别难的，只是需要仔细阅读解决方法。

#### VS403443 错误

此验证错误意味着您需要重命名工作项字段。随着时间的推移，更新的旧数据库似乎会发生这种情况。需要调整模式以与服务同步。

我有大约 8 个这样的问题需要解决，我可以在开发人员命令提示符下使用 witadmin 工具来解决。

```
witadmin changefield /collection:[COLLECTION_NAME] /n:System.IterationId /name:"Iteration Id" 
```

重要的是(我一开始搞砸了),/n 参数是字段，而/name 是您将它更改为的名称。

#### ISVError:100014 Error

此错误意味着其中一个内置组缺少所需的权限。需要使用 TFSSecurity.exe 工具重新添加。

使用[迁移故障排除](https://docs.microsoft.com/en-us/azure/devops/migrate/migration-troubleshooting?view=azure-devops#isverror100014)中的说明来解决此问题。

### 用户

您可能会遇到一些关于用户的验证问题，但是在我的测试运行中，我在导入后修复了我的用户，确保正确的用户可以访问，并删除了不属于我的用户。

在导入后的下一个月 1 日之前，您不会被收取费用，因此您将有时间解决任何用户导入问题。

### 空间

如果你得到一个警告，说你的导入太大了，需要先导入到 Azure SQL 数据库，那么你的导入会变得更加困难。我最初有这个警告，这也是我在导入中清除更多数据的原因(在我们的上一步中)。如果你能控制在这个限度之下，你的生活会更轻松。如果不能，您需要在导入时做一些额外的步骤，我不会做这些，但是我会提供一个如何做的指南的链接。

### 接下来是什么？

我们已经成功验证了导入准备就绪！

接下来，我们将准备实际的迁移包。