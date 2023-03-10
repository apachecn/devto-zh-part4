# 在不到 20 分钟的时间内了解 Terraform 的 6 个基本原理

> 原文：<https://dev.to/loujaybee/learn-the-6-fundamentals-of-terraform-in-less-than-20-minutes-2c1d>

Terraform 是[云原生软件](https://dev.to/loujaybee/what-is-a-cloud-native-software-engineer-j09-temp-slug-3532458)工程师学习的基础工具。在我看来，Terraform 应该(也将会)像 git 这样的工具在版本控制中一样无处不在。

[![](img/b79590aa1b4c7b210a5caf345de093a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-M52xNT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.thedevcoach.co.uk/wp-content/uploads/2019/08/terraform.png%3Ffit%3D760%252C399%26ssl%3D1)

今天，我们将讨论您需要了解的 6 个关键基础主题，以便快速使用 Terraform。我们不会深入讨论这些概念(这是件好事！)但是我们会让你在深入潜水之前知道这个概念是什么以及它是如何工作的。

在本文结束时，你将会意识到 Terraform 的 6 个关键概念。从语言结构到文件格式的一切。

## 什么是 Terraform？

Terraform 是基于客户端的独立[基础设施，作为代码](https://www.thedevcoach.co.uk/infrastructure-as-code-ultimate-guide/) (IaC)工具(不熟悉 IaC？[先看这篇文章](https://dev.to/loujaybee/infrastructure-as-code-a-quick-and-simple-explanation-56ln)。我说的基于客户是什么意思？让我解释一下:Terraform 是从您自己的机器上安装和运行的，而不是一个托管的解决方案。这是一个重要的区别，因为像 [CloudFormation](https://aws.amazon.com/cloudformation/) 这样的工具是在别人的基础设施上运行的。

Terraform 也是一种声明式技术。声明性工具意味着你指定(阅读:*声明* ) **你想要什么**目标最终状态，然后 terraform 制定**如何**去做。作为代码工具的其他基础设施可能以非声明的方式工作，期望您指定您想要什么以及如何做。

## 为什么要学 Terraform？

在我们进入什么是 Terraform 之前，让我们了解一下为什么理解 Terraform 很重要。

Terraform 将帮助你学习其他云工具——我非常鼓励[尝试工具](https://dev.to/loujaybee/your-personal-aws-setup-and-how-to-not-get-hacked-5hnf)，以便最好地学习它们。我发现使用 Terraform 是一种试验云工具的简单方法，同时能够跟踪我做了什么，并在完成后删除所有内容。这确保了我不会留下任何讨厌的惊喜，比如[意想不到的大额账单](https://dev.to/loujaybee/your-personal-aws-setup-and-how-to-not-get-hacked-5hnf)！

**学习基础设施将使你成为更好的工程师** —如果你是一名应用程序开发人员，我认为学习基础设施是一个很好的机会。许多事情可以在基础架构级别完成，但不能在应用程序级别完成。因此，了解您所拥有的基础设施工具可以真正帮助您实现可靠的技术解决方案。

**Terraform 是目前最好的配置工具** — Terraform 是其基础设施配置工具类别中最好的工具。我不会解释为什么是今天，我们将把这个话题留到整篇文章中。尽管如此，如果你打算投资学习一种工具，重要的是要确保这种工具将存在很长一段时间，而有了 Terraform，你就能得到这种保证。

## 了解 Terraform 的主要特性

首先，我想解释一下为什么我们要讨论这 6 个概念。当谈到学习一个像 Terraform 这样的工具时，我们是不是可以更好地一头扎进去写一些代码呢？简单的回答是:没有。

如果你花时间意识到这 6 个主要概念，那么学习 Terraform 对你来说会很容易。如果你只是一头扎进去，当你已经深入学习的时候，你最终只会想要理解这些概念。

那么，我们今天要讲的基本原理是什么？

1.  **语言**—Terraform 语言的基本结构， [Hashicorp 配置语言](https://github.com/hashicorp/hcl) (HCL)以及我们如何使用该语言配置和创建资源。
2.  **计划&应用**——计划是 Terraform 如何知道要做出什么样的改变。使用`terraform plan`和`terraform apply`命令，我们可以安全地改变基础设施。
3.  **陈述**—Terraform 如何了解我们想要管理和改变的基础设施。我们讨论它是如何创建、更新、存储的等等。
4.  **依赖关系解析**—Terraform 如何使用依赖关系图来了解创建基础设施的顺序。
5.  **文件结构** —我们应该如何构建我们的 Terraform 代码和我们的项目布局。
6.  **提供商**—terra form 如何与我们想要在其中/其上创建基础设施的第三方工具挂钩。

这些是我们将要讨论的主要特性，让我们开始吧。

### 一个微小的地形例子