# 保护你的地形状态

> 原文：<https://dev.to/iamstej/protecting-your-terraform-state-6gn>

当应用 Terraform 模板时，正在部署的基础设施的状态存储在本地`terraform.tfstate`文件中。如果你是一个项目的唯一开发者，将它存储在你的项目文件夹或回购中可能对你有用；在一个团队中，您希望确保您的 Terraform 状态的最新版本存储在一个中心位置，而源代码控制并不是最好的解决方案。

您不希望陷入多个开发人员在同一个云基础架构堆栈上工作，并且相互影响的局面。您需要一个解决方案来防止堆栈损坏，保留修订历史，并从项目结构中删除敏感文件(可能在多个桌面上)。

那么我应该把它存放在哪里呢？

# 保存 S3 地形状态

Terraform 允许您使用后端资源将这种状态存储在 S3 存储桶中🎉

## 1。创建 S3 存储桶

在 AWS 中，您需要创建一个 S3 存储桶。

*   导航到 AWS 中的 S3 部分，然后单击“创建存储桶”。

*   输入一个名称并选择您希望设置存储桶的区域，然后单击“下一步”。

*   确保您选中了复选框“版本控制(将对象的所有版本保存在同一个存储桶中。)”，然后点击“下一步”。

*   **重要提示:**确保选择了“阻止公共访问”，这应该是默认设置，然后单击“下一步”。

*   检查您的配置，如果您满意，请单击“创建存储桶”。

## 2。与您的 S3 桶集成

现在您已经创建了您的 S3 桶，您准备创建一个后端资源，它允许 Terraform 存储和读取来自 S3 的状态。

*   在 Terraform 文件所在的目录中，你需要创建一个新文件，你可以随意命名，在本教程中，我们将其命名为`backend.terraform`

*   在这里，你需要有如下的结构:

```
terraform {
    backend "s3" {
        bucket = "{your-bucket-name}"
        encrypt = true
        key = "path/to/state/state.tfstate" # Where you want to store state in S3
        region = "{your-bucket-region}"
    }
} 
```

*   一旦在 terraform 目录中有了这个文件，现在就可以从 S3 存储和读取您的状态了；运行类似于`terraform apply`的命令将会触发这种情况。您将能够看到文件现在存储在 S3 存储桶中指定的`key`目录中。

*   你可以通过导航到你的 terraform 目录中的`.terraform/terraform.tfstate`来确认这已经工作，并查看哪个后端正在被使用。

# 防止并发部署

现在你的地形状态被存储在一个 S3 桶中，我们如何防止多人同时使用一个堆栈呢？🤯

我们将在 DynamoDB 表中保留一个文件锁😁

## 1。创建 DynamoDB 表

在 AWS 中，您需要创建一个 DynamoDB 表。

*   导航到 AWS 中的 DynamoDB 部分，然后单击“创建表”。

*   为 DynamoDB 表命名，并将主键/分区键设置为`LockID`。

*   其余设置可以保留为默认设置。点击“创建存储桶”。

## 2。与 DynamoDB 表集成

现在您已经创建了 DynamoDB 表，您已经准备好将您的后端与它集成，以确保您的 Terraform 状态在应用更改时被锁定。

*   修改您的后端(我们之前创建的文件，在我们的例子中命名为`backend.terraform`)并添加一个新的键:

```
terraform {
    backend "s3" {
        bucket = "{your-bucket-name}"
        dynamodb_table = "{your-dynamodb-table-name}" # This is the new key
        encrypt = true
        key = "path/to/state/state.tfstate"
        region = "{your-bucket-region}"
    }
} 
```

*   就是这样，状态锁定现在在你的 terraform 栈中工作。你可以通过运行`terraform plan`来测试它，当它运行时，在 AWS 中导航到你的 DynamoDB 表，在 items 部分，点击“Search ”,应该会有一个`LockID`;一旦`plan`过程完成，这将消失。

瞧，完成了！💥

有很多关于如何设置的资料，但这是我喜欢做的事情，在很短的时间内，当我想开始写博客时，所以这是我的第一篇关于 dev.to 的文章，尽管它不是这类文章的第一篇！

**权限:**您需要确保您正在使用的 AWS 概要文件拥有正确的权限来访问 S3 存储桶，并对 DynamoDB 表执行正确的操作。这些都可以在这里找到:【https://www.terraform.io/docs/backends/types/s3.html】T2