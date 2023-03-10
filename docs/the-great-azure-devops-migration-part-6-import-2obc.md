# Azure DevOps 大迁移-第 6 部分:导入

> 原文：<https://dev.to/timothymcgrath/the-great-azure-devops-migration-part-6-import-2obc>

就是这个！我们已经完成了导入步骤！这是我们最终将数据转移到 Azure DevOps 服务的时候。

如果你错过了之前的帖子，从这里开始。

我强烈推荐[微软的 Azure DevOps 服务迁移指南](https://azure.microsoft.com/en-us/services/devops/migrate/)。

### 分离收藏

首先，你需要从 TFS 分离集合。不要在 SQL Server 中分离数据库，而是在 Azure DevOps 服务器中分离集合。

要分离集合，请打开 Azure DevOps 管理工具，转到“集合”,然后在要导入的集合上选择“分离”。

### 生成数据库备份

如果您已经设法将导入保持在 30 GB 以下，这一步相当容易。如果没有，您将面临更困难的导入，因为您现在需要将数据库移动到 Azure 中的 SQL Server 数据库。我不会讨论 SQL Server 迁移，因为我没有完成这一步，但是[这里有关于如何完成这一步的指南](https://docs.microsoft.com/en-us/azure/devops/migrate/migration-import?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=azure-devops#importing-large-collections)。

因此，如果您走的是 30 GB 以下的路线，您需要创建一个将被导入 Azure DevOps 服务的 DACPAC。您应该能够从 Visual Studio 的开发人员命令提示符或从以下位置运行 DACPAC 工具:

```
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150 
```

以下是打包命令:

```
SqlPackage.exe /sourceconnectionstring:”Data Source=localhost;
Initial Catalog=[COLLECTION_NAME];Integrated Security=True” 
/targetFile:C:\dacpac\Tfs_DefaultCollection.dacpac 
/action:extract 
/p:ExtractAllTableData=true 
/p:IgnoreUserLoginMappings=true 
/p:IgnorePermissions=true 
/p:Storage=Memory 
```

打包完成后，您将在 C:\dacpac\下有一个新的 DACPAC，其中包含您所有的导入数据。

### 上传包

我们不打算将包直接上传到 Azure DevOps 服务中。首先，我们需要将它上传到 Azure 本身。然后我们将 Azure DevOps 服务指向 Azure 中的 DACPAC。

最简单的方法是安装 [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) 。

打开 Azure Storage Explorer 应用程序。
选择添加 Azure 帐户。
登录您的 Azure 帐户。
去天蓝色的储物容器。
创建一个名为 DACPAC 的新 Blob 容器。
上传 SqlPackage.exe 创建的 DACPAC 文件。

### 创建 SAS 密钥

您需要创建一个允许 Azure DevOps 服务访问 DACPAC 的密钥。

在 Azure 存储资源管理器中，右键单击 DACPAC 文件夹，然后选择“获取共享访问签名”...

将过期时间设置为从今天起一周。
给它读/列表的权利，别无其他。
复制 SAS 密钥的 URL。

这个 SAS URL 应该放在 import.json 文件中，该文件位于前面的 Logs 文件夹中。在源代码中设置它。位置字段。

### 导入

就是这样！我们准备好开始导入了！

从数据迁移工具文件夹中运行以下命令:

```
Run Migrate import /importfile:[IMPORT-JSON-LOCATION] 
```

导入将开始，该命令将提供一个链接来查看您的导入状态。

确实需要几分钟才能看到导入页面，所以不要惊慌。

导入开始后，大约需要两个小时才能完成...所以这是休息的好时机。

### 验证

你做到了！您到 Azure DevOps 的迁移已完成。现在，您应该验证一切工作正常。

#### 用户

首先，验证您的用户列表。您可以在组织设置中找到您的用户。我不得不排除许多不需要访问服务的用户。然后，您应该为实际用户设置正确的访问级别。我们有很多 VS Enterprise 订阅，我为我的大多数开发人员使用过，我们的承包商获得了基本的访问权限。最重要的是，确保应该列出所有用户。

这是一个了解 Azure DevOps 服务实际成本的绝佳机会，因此请确保像您的生产环境一样进行设置。

#### 源代码控制

因为您移动了您的 GIT 源代码控制，所以您实际上不需要重新克隆它，您只需要将您现有的本地 repo 重定向到新的位置。

您可以使用以下命令更改您的本地 repo 源(您可以在 Azure DevOps Service - Repos - Files 中的 Clone 按钮中找到 REMOTE_GIT_REPO)。

```
git remote set-url origin [REMOTE_GIT_REPO] 
```

#### 
  
计费

请确定您的付费帐户已针对该服务进行了配置。当您进行生产迁移时，这一点很重要。你要到下个月 1 号才会收到账单，所以请确保你在月底之前设置好账单和用户。

#### 构建/发布代理

任何本地构建/发布代理都需要重新配置。我只有大约 10 个代理在本地运行，所以我选择删除它们，并在最终生产运行后重新安装它们。Powershell 命令使这变得非常容易。

我没有进行测试，只是在生产迁移后简单地重新配置了一下，一切都很顺利。

### 最终导入

就是这样！我们很少遇到其他问题，试运行进行得很顺利，几周后的生产迁移也非常顺利。

对于最终的迁移，我只是重复了本指南的步骤，并将 import.json 更改为使用生产而不是模拟运行。

我关闭了我们本地的 TFS 服务器，并把它保留下来，以防我们需要遗留代码。

最终迁移后出现的主要问题是正确设置用户权限，但我只是在迁移过程中调整了这些设置。

一些用户遇到了非 Visual Studio 工具无法连接到远程 repo 的问题，但在 Azure DevOps 服务- Repos - Files - Clone 中设置他们的 GIT 凭据修复了该问题。

我希望你从我的努力中学到了什么，如果你有任何问题，请告诉我！