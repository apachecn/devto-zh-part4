# 天蓝色和地形，第二轮

> 原文：<https://dev.to/tonytalkstech/azure-and-terraform-round-two-3oam>

我[最近写了一篇关于使用 Terraform 管理 Azure 资源的博客](https://dev.to/tonytalkstech/azure-and-terraform-1n0l)。老实说，我的实现是*还好*，但它肯定可以改进。这篇文章是关于我如何在项目中更新 Terraform 的结构和用法的更新。

# 项目结构

在任何有 Terraform 资源的项目中，我的文件夹结构如下:

```
project
│   .gitignore
│   azure-pipelines.yml
│   create-storage.sh  
│
└───terraform
    │   data.tf
    │   locals.tf
    │   main.tf
    │   provider.tf
    │   variables.tf
    │   versions.tf
    │
    └───environments
        └───development
            │   terraform.tfvars
        └───staging
            │   terraform.tfvars
        └───production
            │   terraform.tfvars 
```

Enter fullscreen mode Exit fullscreen mode

## 。吉蒂尔

相当标准的`.gitignore`文件在这里。我使用 JetBrains IDEs，所以我引入了 IntelliJ-standard 条目，以及更多条目。[去这里](https://www.gitignore.io/api/terraform,intellij+all,visualstudiocode)找我用的确切的`.gitignore`。

## [T1】azure-pipelines . yml](#azurepipelinesyml)

由于我的资源在 Azure 中，使用 Azure DevOps 来构建和部署管道是有意义的。构建管道是用 [Azure Pipeline 的 YAML 模式](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema?view=azure-devops)明确定义的。不幸的是，发布管道目前只在 Azure Pipelines 的 web UI 中定义(总之，它实际上只是一个`terraform apply`)。

一般来说，我的`azure-pipelines.yml`中的 Terraform 位在各个项目中是相同的。注意，我截断了文件，只包含了`development`环境，但是其他环境基本上是相同的，只是更新了变量。

```
variables:
  AZURE_SUBSCRIPTION: 'xxx'
  BASE_ENVIRONMENT_PATH: 'terraform/environments/$(ENVIRONMENT_NAME)'
  CONTAINER_NAME: $(ENVIRONMENT_PREFIX)terraform
  KEYVAULT_NAME: 'product-terraform-kv'
  KEYVAULT_SECRET_NAME: $(ENVIRONMENT_PREFIX)-storage-account-key
  LOCATION: 'eastus'
  STORAGE_ACCOUNT_NAME: 'productterraform'
  TERRAFORM_PATH: 'terraform'
  TERRAFORM_STATE: 'product_infrastructure.tfstate'
  TERRAFORM_VERSION: '0.12.3'
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

- stage: Test
  dependsOn: Setup
  jobs:
  - job: TestDevelopmentTerraform
    variables:
      ENVIRONMENT_PREFIX: 'd'
      ENVIRONMENT_NAME: 'development'
    displayName: 'Test  Development  Terraform'
    steps:
    - task: AzureKeyVault@1
      displayName: 'Azure  Key  Vault:  $(KEYVAULT_NAME)'
      inputs:
        azureSubscription: '$(AZURE_SUBSCRIPTION)'
        KeyVaultName: '$(KEYVAULT_NAME)'
        SecretsFilter: '$(KEYVAULT_SECRET_NAME)'

    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-installer.TerraformInstaller@0
      displayName: 'Use  Terraform  $(TERRAFORM_VERSION)'
      inputs:
        terraformVersion: $(TERRAFORM_VERSION)

    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'terraform  init'
      inputs:
        command: init
        workingDirectory: '$(BASE_ENVIRONMENT_PATH)'
        commandOptions: '-backend-config="access_key=$(d-storage-account-key)"  -backend-config="storage_account_name=$(STORAGE_ACCOUNT_NAME)"  -backend-config="container_name=$(ENVIRONMENT_PREFIX)terraform"  -backend-config="key=$(TERRAFORM_STATE)"'

    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'terraform  validate'
      inputs:
        command: validate
        workingDirectory: '$(BASE_ENVIRONMENT_PATH)'
        commandOptions: '-var-file=".\environments\$(ENVIRONMENT_NAME)\terraform.tfvars"'

- stage: Package
  dependsOn: Test
  jobs:
  - job: PackageTerraform
    displayName: 'Package  Terraform'
    steps:
    - task: PublishBuildArtifacts@1
      displayName: 'Publish  Terraform  Artifacts'
      inputs:
        pathToPublish: '$(TERRAFORM_PATH)'
        artifactName: tf 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很大的配置，但我会尝试压缩它。管道分为三个独立的阶段:设置、测试和打包。

### 设置阶段

设置阶段解决了我所说的“先有鸡还是先有蛋的问题”归结起来就是要求 Azure 资源存储 Terraform 状态，但是我们不能通过 Terraform 创建那些 Azure 资源，因为它还不知道在哪里存储它。我没有依赖 Terraform 来创建这些资源，而是调用一个单独的脚本。它设置一些环境变量，然后调用位于 source: `create-storage.sh`中的 shell 脚本。这个脚本的内容如下。

```
#!/bin/sh

RESOURCE_GROUP_NAME=product-terraform-rg
STORAGE_ACCOUNT_NAME=productterraform
KEYVAULT_NAME=product-terraform-kv

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location ${LOCATION}

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create Key Vault
az keyvault create --name $KEYVAULT_NAME --resource-group $RESOURCE_GROUP_NAME --location ${LOCATION}

# Store account key in secret
az keyvault secret set --name ${KEYVAULT_SECRET_NAME} --vault-name $KEYVAULT_NAME --value $ACCOUNT_KEY

# Create blob container
az storage container create --name ${CONTAINER_NAME} --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY 
```

Enter fullscreen mode Exit fullscreen mode

脚本本身非常简单。它确保每个给定产品都有一个标准资源组。在该资源组中，它创建一个存储帐户、密钥库、密钥库密码和一个 blob 容器。该脚本从 Azure CLI 提取存储帐户的密钥，并将其存储在密钥库机密中。该密钥将在未来的`terraform init`通话中使用。blob 容器将保存稍后在该过程中创建的 Terraform 状态文件。

如果部署到 Azure 的应用程序需要一个数据库，我有一个稍微修改过的脚本版本，它将生成一个随机的数据库密码，并将其存储在同一个密钥库中，但存储在一个单独的秘密中。下面可以看到那个版本。

```
#!/bin/sh

RESOURCE_GROUP_NAME=product-terraform-rg
STORAGE_ACCOUNT_NAME=productterraform
KEYVAULT_NAME=product-terraform-kv

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION > /dev/null

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob > /dev/null

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create Key Vault
az keyvault create --name $KEYVAULT_NAME --resource-group $RESOURCE_GROUP_NAME --location $LOCATION > /dev/null

# Store account key in secret
az keyvault secret set --name $KEYVAULT_SECRET_NAME --vault-name $KEYVAULT_NAME --value $ACCOUNT_KEY > /dev/null

# Check if database password exists
DB_SECRET_INFO=$((az keyvault secret show --name $DB_PASSWORD_SECRET_NAME --vault-name $KEYVAULT_NAME) 2>&1)

# Create the database password if it doesn't exist
if [[ $DB_SECRET_INFO =~ "(SecretNotFound)" ]]; then NEW_UUID=$(openssl rand -base64 24)
  az keyvault secret set --name $DB_PASSWORD_SECRET_NAME --vault-name $KEYVAULT_NAME --value $NEW_UUID > /dev/null
fi

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY > /dev/null 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这基本上是相同的脚本，但是如果数据库密码还没有生成，则使用了一个小的 UUID 生成器。有很多方法可以生成随机字符串，但是`openssl rand -base64 24`是最简单的(它可以在 Azure Linux worker 机器上工作)。

### 测试阶段

测试阶段安装一个特定版本的 Terraform，借助从先前创建的密钥库中检索的值运行一个`terraform init`，然后运行一个`terraform validate`。

您会注意到`terraform-init`使用了`$(d-storage-account-key)`变量。在此之前的 Azure Key Vault 步骤会将密钥库机密的值提取到该变量中。不幸的是，我还没有发现双重引用变量的方法，所以我必须将它作为硬编码的引用。作为参考，我更希望有像`$($(KEYVAULT_SECRET_NAME))`这样的东西，但目前看来这是不可能的。

`terraform validate`步骤的细节很重要:它直接指向特定于环境的`terraform.tfvars`。这就是我如何用一个代码库完成多环境发布。

### 包阶段

打包阶段是管道中最简单的:它只是运行一个开箱即用的`PublishBuildArtifacts`任务，指向`terraform`目录并将其放入`tf`工件中。这将在稍后的发布管道中使用。

## Terraform 神器

为了便于使用，我已经将 Terraform 工件分解成许多文件。

### data.tf

对于只有基础设施的存储库，这个文件非常简单:

```
data "azurerm_subscription" "this" {
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果给定的存储库是建立在另一个存储库之上的(例如，建立在基础设施特定的存储库之上的应用程序特定的存储库)，这里显然会有其他的`data`块。下面是一个例子。

```
data "azurerm_resource_group" "this" {
  name = local.resource_group_name
}

data "azurerm_app_service_plan" "this" {
  name                = local.app_service_plan_name
  resource_group_name = data.azurerm_resource_group.this.name
}

data "azurerm_storage_account" "this" {
  name                = local.storage_account_name
  resource_group_name = data.azurerm_resource_group.this.name
}

data "azurerm_sql_server" "this" {
  name                = local.sql_server_name
  resource_group_name = data.azurerm_resource_group.this.name
} 
```

Enter fullscreen mode Exit fullscreen mode

### locals.tf

我通常使用`locals.tf`文件来定义我将在许多地方使用的聚合资源名称。

```
locals {
  resource_group_name   = "${var.environment_prefix}-${var.application_name}-rg"
  app_service_plan_name = "${var.environment_prefix}-${var.application_name}-plan"
  scope                 = "/subscriptions/${var.subscription_id}/resourceGroups/${azurerm_resource_group.this.name}"
} 
```

Enter fullscreen mode Exit fullscreen mode

### main.tf

我的`main.tf`是我创建 Azure 资源本身的地方。这个文件没有什么有趣或独特的地方，除了我通常不创建自己的模块来分组项目。在这一点上我还没有一个好的理由。

指出每个存储库**只有一个 main.tf 定义的**可能是有用的。这很重要，因为它暗示了每个环境都有相同类型的 Azure 资源。虽然一切都是变量驱动的，因此资源本身可以进行不同的配置，但每个不同的环境总共会有相同的资源。

### [T1】provider . TF](#providertf)

这里没什么疯狂的。

```
terraform {
  backend "azurerm" {
  }
}

provider "azurerm" {
  version = "~>1.30.1"
} 
```

Enter fullscreen mode Exit fullscreen mode

我试图尽可能多地升级我的提供商和 Terraform 版本，但我通常一次跨 10-15 个库工作，所以一旦我在一个版本上得到所有的库，我会坚持一段时间那个版本。

### variables.tf

同样，这里没什么特别的。想象一下下面的`role_assignments`变量中新的 Terraform v0.12 用法！

```
variable "environment_prefix" {
}

variable "application_name" {
}

variable "location" {
}

variable "subscription_id" {
}

variable "app_service_plan_sku_tier" {
}

variable "app_service_plan_sku_size" {
}

variable "tags" {
  type = map(string)
}

variable "role_assignments" {
  type = list(object({ username = string, object_id = string, role_definition = string }))
} 
```

Enter fullscreen mode Exit fullscreen mode

### versions.tf

我喜欢明确定义给定存储库支持哪个版本的 Terraform。这就是完成的地方。

```
terraform {
  required_version = ">= 0.12"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 地形地貌. tfvars

每个环境都有自己的`terraform.tfvars`文件。这是给定变量(在上面的`variables.tf`中定义)的值被传入的地方，如果它们可以自由公开的话。如果有需要传入的秘密值，它们将存储在一个密钥库中，并在发布管道中提取，类似于上面的存储帐户密钥。

```
environment_prefix        = "d"
application_name          = "product"
location                  = "eastus"
subscription_id           = "xxx"
app_service_plan_sku_tier = "Shared"
app_service_plan_sku_size = "D1"

tags = {
  "terraform"   = "true",
  "environment" = "Development",
  "application" = "Product"
}

role_assignments = [
  {
    username        = "xxx"
    object_id       = "xxx",
    role_definition = "Owner"
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

# 释放管道

如前所述，Azure DevOps 有一个限制，它只允许用浏览器内 UI 编辑发布管道。这个**很烂**，但是我已经习惯了。

任何给定项目的发布管道通常看起来都是一样的:

1.  从 Azure Key Vault 提取机密
2.  安装地形
3.  运行`terraform init`
4.  运行`terraform apply`

然后，如果管道需要，并且有要部署的应用程序:

1.  将 Terraform 输出设置为 Azure 管道变量
2.  将应用程序部署到 Azure 应用服务
3.  根据需要设置管道变量的值

本节有意忽略细节，因为没有太多内容可谈。

# TL；博士；医生

总而言之，在过去的 7 个多月里，我对 Azure 平台的态度有了很大的改变。现在，我不再为每个环境定义资源，而是将资源创建整合到一个文件中，并在每个环境目录中设置变量。同样，这显然是因为我没有一个用例要求每个环境有**不同的资源**。

除了项目结构的变化，“鸡和蛋的问题”已经在 Azure Pipeline 本身内部得到了解决。我现在可以依靠管道本身来管理后台数据存储，而不必在第一次运行 Terraform 之前手动创建资源。这是我在 Azure DevOps 中运行管道的最大改进。

和往常一样，如果你想更直接地聊些什么，可以在 [Twitter](https://twitter.com/tonytalkstech) 上联系我，因为那是我最活跃的地方。