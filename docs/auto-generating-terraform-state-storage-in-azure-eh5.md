# Azure 中自动生成地形状态存储

> 原文：<https://dev.to/tonytalkstech/auto-generating-terraform-state-storage-in-azure-eh5>

在我最新的 [Azure/Terraform 帖子](https://dev.to/tonytalkstech/azure-and-terraform-round-two-3oam#setup-stage)中，我谈到了我如何用 Terraform 解决“鸡和蛋”的问题:你如何需要云资源来存储 Terraform 状态，但你不能使用 Terraform 来生成那些云资源。这篇文章详细介绍了这个问题的解决方案。

# 问题

对我来说，Terraform 的“先有鸡还是先有蛋”的问题可以简单地用四点来定义:

1.  我想编写 Terraform 来在云提供商中生成资源。
2.  我不想在本地运行 Terraform。
3.  我想将我的 Terraform 状态存储在同一个云提供商中。
4.  我如何生成存储我的地形状态的云提供商资源？

# 解

您可以使用云提供商的原生工具来生成和管理这些资源，而不是依靠 Terraform 来生成存储您的 Terraform 状态的云提供商的资源。由于我目前在 Azure 工作，我将使用 Azure 的[CLI 工具](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。这种类型的解决方案也与 [AWS 的`aws` CLI](https://aws.amazon.com/cli/) 和 [GCP 的`gcloud` CLI](https://cloud.google.com/sdk/gcloud/) 相关，如果你想使用它们的话。

## 脚本

在我的每个存放 Terraform 定义的存储库中(最终是每个存储库)，我都有一个额外的脚本:`create-storage.sh`。

这是一个 Bash 脚本，因为我使用。NET Core，我的构建/部署机器在 Azure DevOps 中都是基于 Linux 的。如果需要在 Windows 环境中执行，这也可能是一个 PowerShell 脚本。

抬起头来！:如果你想要最新版本的脚本，请[到这里](https://github.com/afmorris/TerraformAutoGenStorage/blob/master/Azure/create-storage.sh)而不是相信这个静态网站。

```
#!/bin/sh

# Heads up! You need to define the following environment variables:
# RESOURCE_GROUP_NAME for the resource group that will contain the Azure Storage Account that will house your Terraform state files
# STORAGE_ACCOUNT_NAME for the name of the Azure Storage Account
# KEYVAULT_NAME to store the Storage Account's access key, so you don't have to manually keep track of it
# LOCATION for the location of the Azure resources
# KEYVAULT_SECRET_NAME for the name of the secret in Key VAult of the Storage Account's access key
# CONTAINER_NAME for the Azure Blob Storage's container that will hold the Terraform state file(s)

RESOURCE_GROUP_NAME=mm-terraform-rg
STORAGE_ACCOUNT_NAME=mmterraform
KEYVAULT_NAME=mm-terraform-kv

# Create resource group
az group create --name ${RESOURCE_GROUP_NAME} --location ${LOCATION}

# Create storage account
az storage account create --resource-group ${RESOURCE_GROUP_NAME} --name ${STORAGE_ACCOUNT_NAME} --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group ${RESOURCE_GROUP_NAME} --account-name ${STORAGE_ACCOUNT_NAME} --query [0].value -o tsv)

# Create Key Vault
az keyvault create --name ${KEYVAULT_NAME} --resource-group ${RESOURCE_GROUP_NAME} --location ${LOCATION}

# Store account key in secret
az keyvault secret set --name ${KEYVAULT_SECRET_NAME} --vault-name ${KEYVAULT_NAME} --value $ACCOUNT_KEY

# Create blob container
az storage container create --name ${CONTAINER_NAME} --account-name ${STORAGE_ACCOUNT_NAME} --account-key $ACCOUNT_KEY 
```

### 环境变量

上面的脚本需要几个环境变量。

| 可变的 | 目的 |
| --- | --- |
| `RESOURCE_GROUP_NAME` | 将容纳为 Terraform 状态存储生成的所有资源的资源组的名称 |
| `LOCATION` | 资源组的位置(以及位于资源组中的后续资源) |
| `STORAGE_ACCOUNT_NAME` | 我们将在其中创建 blob 存储的 Azure 存储帐户的名称 |
| `CONTAINER_NAME` | Azure Blob 存储中 Azure 存储容器的名称。这实际上会保存地形状态文件 |
| `KEYVAULT_NAME` | 为存储 Azure 存储帐户密钥而创建的 Azure 密钥库的名称。这允许我们自动运行 Terraform 文件，而不必自己存储密钥 |
| `KEYVAULT_SECRET_NAME` | 将存储 Azure 存储帐户密钥的机密名称 |

请特别注意，我们正在生成 Azure Key Vault 和 Azure Key Vault Secret 来管理 Azure 存储帐户密钥的存储。虽然这在技术上是不必要的，并且我们可以在需要时随时查询 Azure 存储帐户本身的密钥(如脚本的**获取存储帐户密钥**部分所示)，但 Azure DevOps 与 Azure Key Vault 紧密集成，这一步骤简化了我们未来对 Terraform 资源的部署。

### 流程

文件本身的功能非常相似。该文件本身并不特别，因为它与 Azure 在他们的 docs 中提供的内容基本相同。有趣的地方在于，它将存储帐户的密钥导出到密钥库中，以便稍后在部署时在 Azure 管道中使用。

## 管道

这个文件的存在是有用的，但是它没有真正地将资源的交付和真实场景中的“自动生成”部分联系在一起。为此，我在 Azure DevOps 产品中使用了 Azure Pipelines。

### 建立管道

因为 Azure Pipelines 可以使用 YAML 来定义构建管道，所以我在每个库的根目录下都有一个`azure-pipelines.yml`文件。在 YAML 文件中，我得到了下面的代码片段:

```
variables:
  AZURE_SUBSCRIPTION: 'xxx'
  APPLICATION: 'xxx'
  CONTAINER_NAME: $(ENVIRONMENT_PREFIX)terraform
  KEYVAULT_NAME: $(APPLICATION)-terraform-kv
  KEYVAULT_SECRET_NAME: $(ENVIRONMENT_PREFIX)-storage-account-key
  LOCATION: 'eastus'
  RESOURCE_GROUP_NAME: $(APPLICATION)-terraform-rg
  STORAGE_ACCOUNT_NAME: $(APPLICATION)terraform
  TF_IN_AUTOMATION: 'true'

stages:
- stage: Setup
  jobs:
  - job: SetupDevelopmentStorage
    variables:
      ENVIRONMENT_PREFIX: 'd'
      ENVIRONMENT_NAME: 'development'
    displayName: 'Setup  Development  Storage'
    steps:
    - task: AzureCLI@1
      displayName: 'Run  Setup  Script'
      inputs:
        azureSubscription: $(AZURE_SUBSCRIPTION)
        scriptPath: './create-storage.sh'
  - job: SetupStagingStorage
    variables:
      ENVIRONMENT_PREFIX: 's'
      ENVIRONMENT_NAME: 'staging'
    displayName: 'Setup  Staging  Storage'
    steps:
    - task: AzureCLI@1
      displayName: 'Run  Setup  Script'
      inputs:
        azureSubscription: $(AZURE_SUBSCRIPTION)
        scriptPath: './create-storage.sh'

  - job: SetupProductionStorage
    variables:
      ENVIRONMENT_PREFIX: 'p'
      ENVIRONMENT_NAME: 'production'
    displayName: 'Setup  Production  Storage'
    steps:
    - task: AzureCLI@1
      displayName: 'Run  Setup  Script'
      inputs:
        azureSubscription: $(AZURE_SUBSCRIPTION)
        scriptPath: './create-storage.sh' 
```

需要说明的是，这个构建管道中没有什么特别的东西。它只是执行`create-storage.sh`脚本，并传入不同的特定于环境的参数。您可以看到，我在其他环境变量的定义中使用了特定于环境的参数(例如，`CONTAINER_NAME`在其定义的开头有`$(ENVIRONMENT_PREFIX)`)。这让我在同一个 Azure 存储帐户中拥有单独的容器，存放特定于环境的 Terraform 状态文件。或者，我可以使用不同命名的 Terraform 状态文件，但是我喜欢文件名本身的一致性。

### 部署管道

这就是奇迹发生的地方。在撰写本文时，我目前还没有使用[多级管道](https://devblogs.microsoft.com/devops/whats-new-with-azure-pipelines/)，所以这些都是在经典的发布管道 web UI 中完成的。

发布管道的第一步是通过`AzureKeyVault`步骤从`create-storage.sh`脚本中检索存储的密钥库秘密。然后，它被存储在一个以秘密名称命名的 Azure Pipelines 变量中。例如，如果秘密名是`d-storage-account-key`，Azure Pipeline 的变量也将是`d-storage-account-key`。我们将在未来的步骤中看到它的使用。

对于特定于 Terraform 的步骤，我使用了 Azure Marketplace 上由 Charles Zipp 编写的非常精彩的 [Terraform 构建和发布任务。](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform)

我使用的第一个 Terraform 步骤仅仅是使用`TerraformInstaller`步骤将 Terraform 安装到代理。在撰写本文时，我使用的是 Terraform v0.12.3，但我相信这很快就会改变。

第二个地形步骤是通过使用`TerraformCLI`步骤运行`terraform init`。我传入以下命令选项:`-backend-config="access_key=$(d-storage-account-key)" -backend-config="storage_account_name=$(APPLICATION)terraform" -backend-config="container_name=$(ENVIRONMENT_PREFIX)terraform" -backend-config="key=$(APPLICATION).tfstate"`。

正如我之前提到的，你会看到我正在使用`$(d-storage-account-key)` Azure Pipeline 变量来检索访问键。直截了当，但如果你正在构建它，要记住一些事情。

最后的地形步骤是通过再次使用`TerraformCLI`步骤运行`terraform apply`。我传入的唯一命令选项是特定于环境的 Terraform 变量:`-var-file="./environments/$(ENVIRONMENT_NAME)/terraform.tfvars"`。这些变量的用法可以在[之前的 Azure/Terraform 帖子](https://dev.to/tonytalkstech/azure-and-terraform-round-two-3oam#terraform-tfvars)中看到。

# TL；博士；医生

使用原生云提供商 CLI 工具来生成和存储访问密钥，以便稍后在构建和部署管道时使用。很简单，是吗？