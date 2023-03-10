# 什么是 Terraform？

> 原文：<https://dev.to/securitylater/what-is-terraform-43f7>

### 简介

这篇文章涵盖了什么是 Terraform，使用它有什么好处。

Terraform 是一个允许您定义和创建资源的工具。Terraform 实现这一点的方式是最好的部分。Terraform 使用带有配置数据的易读语句来创建这些资源。

以这种方式管理您的资源被称为代码为的**基础设施。您现在不是通过交互式配置工具来创建和管理资源及其配置，而是通过机器可读的 Terraform 文件来完成。**

### 举例

如果您想使用 Terraform 在 GitHub 上创建一个存储库，您可以创建一个包含以下内容的 terraform 文件:

```
provider "github" {
  token        = "${var.github_token}"
  organization = "${var.github_organization}"
}

resource "github_repository" "my_awesome_repo" {
  name        = "my_awesome_repo"
  description = "My awesome repo"
  private     = false
} 
```

这是告诉 Terraform 创建一个 GitHub 存储库，名称为“my_awesome_repo”，描述为“My awesome repo”。当您应用此配置时，Terraform 将为您创建该资源。就这样！

### 好处

> “我为什么需要这个？我可以使用 python 或我最喜欢的高级语言轻松编写脚本。”

确实如此。然而，Terraform 提供了许多开箱即用的特性，这些特性是您无法通过自己编写资源创建脚本来获得的。

*   Terraform 能够保持状态。它知道你创建了什么资源，需要创建什么资源，需要更新什么资源，需要删除什么资源。

*   Terraform 有一个`plan`命令，当它运行时会告诉你什么资源将被创建、修改或删除。这真的很方便，可以确保你正在创造你想要创造的东西，而且没有惊喜。

*   Terraform 可以绘制你的资源图，告诉你哪些资源有依赖关系。这给了你，操作者，很多关于你的资源的洞察力。

*   Terraform 并行创建和修改无依赖关系的资源，以高效地构建基础架构。

你从 Terraform 得到的最大好处之一是**工作流的可移植性**。无论你是在 GitHub、BitBucket、AWS、Azure、DigitalOcean 还是其他什么网站上使用资源，你的工作流程看起来都是一样的*，无论你使用的是什么服务或提供商*。

1.  创建 Terraform 配置文件
2.  您运行`terraform init`来初始化当前目录
3.  您运行`terraform plan`来查看将会创建什么资源
4.  您运行`terraform apply`来创建这些资源

相比之下，您必须知道在哪里寻找以及如何配置您正在单独工作的每个服务。

Terraform 很棒，有很多好处。我强烈鼓励任何对基础设施或 devops 感兴趣的人立即开始学习，因为它是您工具箱中的一个很好的工具。

### 资源&了解更多

[学习 terra form](https://learn.hashicorp.com/terraform/)
[terra form Docs](https://www.terraform.io/docs/index.html)
[terra form 综合指南](https://blog.gruntwork.io/a-comprehensive-guide-to-terraform-b3d32832baca)