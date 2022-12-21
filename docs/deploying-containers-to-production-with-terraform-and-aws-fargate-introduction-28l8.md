# 使用 Terraform 和 AWS Fargate 将容器部署到生产环境中——简介

> 原文：<https://dev.to/ianrodrigues/deploying-containers-to-production-with-terraform-and-aws-fargate-introduction-28l8>

随着这些天来 [Docker](https://docker.io) 的出现，建立一个本地开发环境来构建我们的应用程序变得非常简单。如果我们能够同样轻松地将相同的容器部署到生产环境中，这个数字甚至会更高。

这不仅是可能的，因为你将学会如何去做。今天，我将开始一系列的帖子，向大家展示如何在 AWS 上提供必要的基础设施，以启动和运行您的容器化应用程序。在第一部分，我将解释一些关键概念，比如什么是 AWS EC2 容器服务和 AWS Fargate，以及如何使用 Terraform 编写基础设施代码。

我们开始吧！

## 首先，什么是 ECS？

嗯， [EC2 容器服务](https://aws.amazon.com/ecs/) (ECS)是一个 AWS 服务，负责编排 Docker 容器集群。它是 Docker Swarm 和 Kubernetes 的高性能和引人注目的替代产品。

有了它，您不必安装或管理编排软件，也不必管理或扩展虚拟机或容器，因为它会为您抽象所有这些。

围绕 ECS 的一些关键概念是:

*   集群:一组运行容器的 EC2 实例；
*   **任务定义**:描述一个或多个容器的 JSON 文件；
*   **任务**:集群内任务定义的实例化；
*   **Service** :它负责实例化和维护在您的集群上运行的给定数量的任务。

#### 【无服务器】带 Fargate 的容器

最初，您需要设置一个 EC2 实例集群，容器在其中运行。您必须选择实例类型，管理自动扩展，并优化群集利用率。然而，事情发生了变化。

2017 年，AWS 推出了 [Fargate](https://aws.amazon.com/fargate/) ，这是一个计算引擎，使您可以运行容器，而无需担心底层基础设施。现在，您可以设置 ECS 群集，而无需管理服务器。有了 Fargate，你只需要考虑容器。它消除了管理 EC2 实例的需要。您不再需要处理服务器或配置。现在，你只能关注应用程序本身。

## 基础设施即代码和地形

此时，您可能已经注意到 ECS/Fargate 是由许多小部件组成的。此外，有必要提供环境的整个底层部分。这里是[地形](https://terraform.io)的用武之地。

Terraform 是一个安全有效地构建、更改和版本控制基础设施的工具。它可以管理现有的和受欢迎的服务提供商以及定制的内部解决方案。有了它，您可以在配置文件中描述您的基础架构，并利用相同的 Terraform 配置来设置相同的试运行、QA 和生产环境。

#### 基础 Hello World！

解释 Terraform 如何工作的最好方式是用行动来展示它。让我们使用 Terraform 在 AWS 上提供一个`t2.micro` EC2 实例。

我假设你已经安装了 Terraform。如果你没有，[查一下他们的网站](https://learn.hashicorp.com/terraform/getting-started/install.html)。

首先，在您选择的目录*(它是您的工作目录)*中创建一个名为 main.tf *(您可以使用任何名称)*的文件，内容如下:

```
provider "aws" {
  profile = "default"
  region  = "us-east-1"
}

resource "aws_instance" "webserver" {
  ami           = "ami-2757f631"
  instance_type = "t2.micro"
} 
```

文件准备就绪后，您需要运行`terraform init`来初始化工作目录。这通常是编写新的 Terraform 代码或克隆新的存储库之后的第一步，因为它会下载所有必要的提供商插件来供应您的基础架构。

```
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "aws" (terraform-providers/aws) 2.18.0...

Terraform has been successfully initialized! 
```

太好了，Terraform 已经初始化了你的工作目录，你可以开始了。下一步是创建一个执行计划，即 Terraform 将分析您的基础架构的*【当前】*状态，然后确定实现*【期望】*状态需要做什么。它将在屏幕上打印所有这些必需的操作，供您查看。

```
$ terraform plan

...

Terraform will perform the following actions:

  # aws_instance.webserver will be created
  + resource "aws_instance" "webserver" {
      + ami                          = "ami-2757f631"
      + instance_type                = "t2.micro"
      ...

Plan: 1 to add, 0 to change, 0 to destroy. 
```

在检查了执行计划之后，是时候应用这些更改了。Terraform 将再次打印所需的操作，并询问您是否要继续并执行这些操作。键入`yes`然后放手。

```
$ terraform apply

...

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes 
```

恭喜你！您已经使用 Terraform 提供了您的第一个资源。这是一个非常简单的例子，但是，想象一下提供一个由 EC2 实例、数据库和缓存集群组成的集群。或者，想象一下使用相同的代码为试运行、QA 和生产环境提供相同的资源。这肯定会节省很多时间。

## 结论

AWS EC2 容器服务和 AWS Fargate 是实用的工具，可以帮助您部署 dockerized 应用程序，并且通过编写 Terraform 配置文件来提供基础设施是一项非常简单的任务。

在下一篇博文的[中，你将看到我将部署的应用程序，以及我如何构建它，以便能够在本地和云上运行它。下次见！](https://dev.to/ianrodrigues/deploying-containers-to-production-with-terraform-and-aws-fargate-containerization-2eb5)