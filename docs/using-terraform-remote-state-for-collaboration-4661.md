# 使用 Terraform 远程状态进行协作

> 原文：<https://dev.to/theodesp/using-terraform-remote-state-for-collaboration-4661>

[**阅读原文**](https://codethat.today/tutorial/terraform-remote-state-for-collaboration/)

Terraform 是 DevOps 中事实上的工具，它有助于实现基础设施即代码部署。它可以通过人机友好的定义文件和插件的组合来管理和提供软件资源或提供商，这些文件和插件扩展了平台，允许不同的提供商激活他们的 API。

这种基础设施配置由 Terraform 在一个状态文件中管理，这是任何类型的 IT 服务拓扑或云架构的关键事实来源。

Terraform 支持和推广的一个特殊功能是协作。Terraform 支持基于团队的工作流，具有一种称为远程后端的功能。后端是我们可以在一个远程但共享的空间中存储这个 Terraform 状态文件(或简称为 tfstate)的地方，这样团队的任何成员都可以使用它来管理基础设施。

在本文中，我们将看到如何启用这一有用的功能，并使用它来允许团队相互协作，以交付满足业务需求的可靠基础设施。

## 启用远程状态

为了顺利协作，每个团队成员都需要能够访问共享位置的同一个 Terraform 状态文件。

实现这一点很容易。您需要有一个支持锁定状态文件的合适的后端。现在最方便的是 S3 后端，但是你也可以使用 HTTP，etcd 或者 consul 后端。这里是全局配置，假设我们已经配置了一个 S3 存储桶策略:

```
terraform {
  backend “s3” {
    encrypt = true
    bucket = "terraform-remote-state-storage"
    region = “us-east-1”
    key = terraform/state
    dynamo_table = “terraform-state-lock”
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

其中:

*   **桶**:是 S3 桶的名字
*   **键**:TF state 文件的名称
*   **加密**:加密数据
*   **dynamo_table** :状态锁定的 DynamoDB 表

要启用 S3 锁定，您需要将它与一个 DynamoDB 表结合使用，该表将用于锁定状态文件。我们需要这样做的原因是为了防止同时运行 Terraform，从而导致冲突。假设您已经设置了 DynamoDB 策略，那么您需要添加以下资源:

```
resource "aws_dynamodb_table" "dynamodb-terraform-state-locking" {
 name = "terraform-state-lock"
 hash_key = "LockID"
 read_capacity = 10
 write_capacity = 10
 attribute {
   name = "LockID"
   type = "S"
 }
 tags {
   Name = "DynamoDB Terraform State Locking"
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

该表的名称必须与 S3 后端的名称相匹配。

## 使用远程状态

启用远程状态只是完成了一半工作。要在实际例子中启用它，您需要将它用作数据源。例如，当您的基础架构团队正在处理特定的域(设置网络、可用性区域、VPC 或 DNS)时，将它们作为独立的状态是有意义的。

例如，当我们在一个输出变量中暴露 VPC id:

```
output "vpc_id" {
 value = "${aws_vpc.main.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

使用远程状态后端，另一个团队可以做到这一点:

```
data "terraform_remote_state" "vpc" {
 backend     = "s3"
 config {
   bucket = "terraform-remote-state-storage"
   key    = "terraform/state"
   region = "eu-east-1"
 }
}
module "app" {
 source  = "./child"
 name    = "app"
 vpc_id  = "${data.terraform_remote_state.vpc.vpc_id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个团队可能会调配数据库和应用程序存储凭证:

```
output "address" {
  value = "${aws_db_instance.mysql.address}"
}
output "port" {
  value = "${aws_db_instance.mysql.port}"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后另一队可以这样消耗:

```
data "terraform_remote_state" "db" {
  backend = "s3"
  config {
    bucket = “terraform-remote-state-storage”
    key    = “terraform/state/mysql”
    region = "us-east-1"
  }
}
module "app" {
 source  = "./child"
 name    = "app"
 mysql_address  = "${data.terraform_remote_state.db.address}"
 mysql_port  = "${data.terraform_remote_state.db.port}"
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的优点很多；我们希望有一个干净的、可重用的代码库，它可以映射到基础设施，并且可以在不同的环境之间共享。

## devo PS 协作时间

当基础设施根据团队之间共享的代码来定义时，它可能成为需要维护的工件。

不管代码库的类型如何，团队都应该定义指导方针和要遵循的代码质量评审。最终目标是使生产部署变得容易，并且尽可能无摩擦。开发和运营团队可以以创造性的方式合作，减少运营延迟并改善沟通。