# Azure DevOps 大迁移-第 5 部分:准备

> 原文：<https://dev.to/timothymcgrath/the-great-azure-devops-migration-part-5-prepare-1g7f>

我们已经验证了我们的数据可以导入了。现在，我们需要准备要导入的数据！这是一小步，让我们享受这一步的轻松。

如果你错过了之前的帖子，从这里开始。

我强烈推荐[微软的 Azure DevOps 服务迁移指南](https://azure.microsoft.com/en-us/services/devops/migrate/)。

### 准备命令

与我们之前使用迁移器验证命令的方式相同，我们需要运行迁移器准备命令。这将重新运行验证，但也会创建一个. json 文件，用于实际的导入过程。

因此，打开 Powershell 到包含 Migrator.exe 文件的目录(在 DataMigrationTool 下载中)。执行下面的命令:

```
Migrator prepare /collection:[COLLECTION_ADDRESS] tenantdomainname:[AZURE_TENANT_NAME] /region:CUS 
```

我建议使用您的集合的本地主机地址来验证您是否指向了正确的服务器。租户域名是 Azure Active Directory，它将为您新导入的数据连接到该目录。该区域必须来自 Azure 区域的狭窄列表，请确保选择受支持的区域。点击查看完整列表[。](https://azure.microsoft.com/en-us/services/devops/migrate/)

执行该命令，您将看到类似于之前验证运行的结果。

如果一切顺利，您将在 DataMigrationTool 的 Logs 文件夹中找到新的 import.json 文件。在日志中，打开最新的文件夹，并在文本编辑器中打开 import.json 文件。

这个文件中有很多字段，但是我们只关心最上面的那些。更新以下字段:
目标。名称-将在 Azure DevOps 上创建的组织的名称。
属性。ImportType -为该初始测试进行试运行。
这两个源字段将在下一篇文章中更新。

### 天蓝色储物

接下来，您需要设置一个 Azure 存储容器。在将包含所有 TFS 数据的文件导入 Azure DevOps 服务之前，您将把该文件移动到此位置。

在 Azure 中，你只需要创建一个新的标准存储容器。这个容器必须在您在 import.json 文件中设置的同一数据中心区域中创建。所以一定要注意这一点！

我只是在美国中部简单地创建了一个标准的存储容器。

### 接下来是什么？

我们很接近了！我们的数据现在可以导入了！

在下一步中，我们将把数据推到存储容器中，并开始导入过程！