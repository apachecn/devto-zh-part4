# 可组合的基础设施

> 原文：<https://dev.to/bennysbanter/composable-infrastructure-49lb>

## 第一部分:引言

所以你想推出一款应用？你登录到这个叫做 AWS 的东西上，迎接你的是无穷无尽的特性、资源、服务和文档。

将目光放回大脑后，您开始阅读并研究如何创建 EC2 实例。然后，您可能需要创建一个自动扩展组，然后将其分布到多个可用性区域，在其前面放置一个负载平衡器，配置一两个数据库，创建一些 S3 存储桶，并定义一系列 IAM 策略来安全地促进所有这些事务。

你看起来不错！哦，等等，一切都可以在互联网上访问，因为你已经在默认的 VPC 这样做了！

很好，所以现在你需要把它全部转移到私有网络。这将包括更多的文件和阅读有关网络路由，CIDR 块，DNS，TLS，NAT 网关，名单继续下去。完成所有这些设置后，您可能希望将所有内容复制到多个区域，并最终获得一些监控和可观察性。

哇，你想做的只是启动一个应用程序！

学习时，在 AWS 控制台中做上述所有工作是正常的，我做到了，我们都做到了。指向和点击对于学习来说是极好的，但当您开始调配易受频繁更改影响的生产基础架构、有多人在其上工作以及对可再现性的需求不断增加时，这就不那么好了。这就是“基础设施即代码”的由来。

虽然将基础设施配置为代码是一项重大改进，但仍然需要对所有代码进行大量的手动工作和维护。然后是代码的可复制性；当我们想要部署多个环境或资源时会发生什么？我确信我不需要向你解释代码重复的后勤工作有多痛苦。

打住，让我们回到起点；我的商业目标是推出一款应用，而不是与没完没了的基础设施争论不休。

肯定有更好的方法吧？

是的，有，它被称为“可组合的基础设施”。

## 第二部分:可组合的基础设施

那么什么是可组合的基础设施呢？首先，让我们快速了解一下目前应用最广泛的两种主要基础设施。

我们有基础架构即服务(IaaS)。比如 AWS 和 GCP。它们为您提供了 CPU、内存、磁盘、网络、服务器、数据库等等的所有旋钮和开关，但是将它们放在一起是由您自己决定的。

然后是平台即服务(PaaS)。比如 Heroku 和 Docker Cloud。PaaS 抽象并隐藏了原始基础设施的复杂性，通常位于 IaaS 之上。现在你有了一个简单的 API 以下是如何部署您的应用程序或数据库，而不需要知道如何将它们连接在一起。

PaaS 听起来很棒，对吧？嗯，是也不是。通过设计，PaaS 将这些电线隐藏在引擎盖下，因此具有局限性。您必须在 PaaS 的范围内工作，因此受限于支持的语言和协议；您不能直接访问底层资源，也不能监控它们，调试问题变得更加困难，并且随着应用程序的扩展，定制您的架构变得更加困难。

你会看到创业公司和新公司产品的趋势。他们从 PaaS 开始，并利用它提供的简单性来获得产品，但随着他们的发展，他们开始遇到上述限制，并倾向于退回到 IaaS。

我们需要的是 PaaS 提供的部署简单性和 IaaS 提供的深入了解的能力。

这就是可组合基础设施出现的地方。为了解释这一点，我们用一个汽车制造厂来类比。

当创建一个新的汽车模型时，我写下我需要的零件的清单，去仓库从货架上取回那些零件，然后将它们送入生产线进行组装。

这些零件中有许多已经制造出来，随时可以组装。例如，我不想为每辆车制造新的引擎、变速器和散热器。我要这些零件随时可用。通过这样做，它简化了生产，节省了时间，确保了一致性，并为每辆生产的汽车提供了相同的质量。

这是可组合基础设施背后的思维模式。我们可以预先构建这些底层资源并随时投入使用，而不是每次构建平台时都必须手动编码或提供资源。现在，我们只需列出我们需要的东西(例如，EC2 实例、S3 桶和 RDS 数据库)，从我们的可组合基础设施库中取出它们，并将其放入要构建的 CI/CD 中。

现在，我们可以创建简单、一致且可复制的平台 PaaS 的简单性和 IaaS 的可调整性。

## 第三部分:什么样子

为了演示可组合的基础设施，我选择使用 [Terraform](https://www.terraform.io) ，因为它是一个优秀的基础编码工具，适合这种风格的工作流。

在 Terraform 中，我们可以创建我们所谓的[模块](https://www.terraform.io/docs/modules/index.html)。最简单的形式是，模块是我们可以从另一个位置调用和执行的任何一段 Terraform 代码。把它当成一个蓝图。让我告诉你我的意思。

假设我们有一个包含两个子目录和几个空白 Terraform 文件的目录，如下所示:

```
/infracode
  /infracode/my_module/ec2.tf
  /infracode/my_deployments/my_instances.tf 
```

Enter fullscreen mode Exit fullscreen mode

在`ec2.tf`中，我们编写了一些 Terraform 来提供一个简单的 EC2 实例:

```
provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "web" {
  ami = "ami-abcd1234"
  instance_type = "t2.micro"

  tags = {
    Name = "my-instance"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们已经定义了我们的 Terraform 提供者，并定义了通过 AWS 提供者创建的资源(在本例中是 EC2 实例)。酷，所以如果我们要从`/my_module`中运行`terraform apply`，它会关闭并构建一个名为`"my-instance"`的实例。

然而，我们实际上想要做的是在任何需要创建实例的时候重用这些代码。所以让我们使用这个现有的代码作为一个模块。

在我们的另一个目录`/my_deployments`中的`my_instances.tf`文件中，我们没有重写代码，而是像这样做:

```
module "foo" {
  source = "../my_module"
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们已经告诉 Terraform 我们想要使用一个模块，我们给它一个名字，告诉 Terraform 在哪里可以找到我们想要执行的源代码。现在，如果我们从`/my_deployments`中运行`terraform apply`，它会从我们的源代码`../my_module`中读取代码并执行它。很酷，对吧？

如果我想创建两个实例呢？容易；创建第二个模块块:

```
module "foo" {
  source = "../my_module"
}

module "bar" {
  source = "../my_module"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行一个`terraform apply`，它识别出有两个模块，`"foo"`和`"bar"`，并创建它们。

然而，您可能已经注意到，在这一点上，我们创建了两个 EC2 实例，都带有名称标签`"my-instance"`。所以我们需要开始定制每个实例的参数。为此，我们使用变量。所以让我们回到`/my_module`目录中的基本代码`ec2.tf`并做一些修改:

```
provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "web" {
  ami = "ami-abcd1234"
  instance_type = "t2.micro"

  tags = {
    Name = "${var.instance_name}"
  }

variable = instance_name {}
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经将 EC2 名称标签设置为一个名为`instance_name`的空白变量。因此，如果我们回到模块块中的`my_instances.tf`，我们现在可以这样做:

```
module "foo" {
  source = "../my_module"

  instance_name = "my-instance-1"
}

module "bar" {
  source = "../my_module"

  instance_name = "my-instance-2"
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在两个模块块中定义了`instance_name`，它们的值被插入到根模块`ec2.tf`中的空白变量`instance_name`中。Terraform 现在将创建两个实例，一个带有名称标签`"my-instance-1"`，另一个带有`"my-instance-2"`。我们现在有了一个可重复的模块，可以在任何需要的时候创建 EC2 实例。

虽然这只是一些基础设施的简单例子，但它展示了使用 Terraform 模块创建可组合基础设施的基础和概念！

最好的部分是，作为一个社区，我们可以共同努力，相互受益。有了这个前景，我们就有了奇妙的资源，比如 Terraform Module Registry，作为一个社区，我们可以编写和共享模块，供彼此在我们的平台创作中使用。当我们可以从彼此的工作中获益时，我们写同样的东西是没有意义的！

当我们创建许多模块并将它们链接在一起以创建一个完整的部署时，这个过程的真正威力就发挥出来了。例如，如果我们想要部署 Hashicorp 的[保险库](https://www.vaultproject.io/)，而不是构建相对复杂的基础设施来运行它，我们可以简单地使用现有的可组合基础设施，如[这个保险库模块](https://registry.terraform.io/modules/hashicorp/vault/aws/0.13.2)，来为我们构建它。现在，我们真正拥有了 PaaS 的部署简单性，能够深入了解 IaaS 层的源代码。

## 第四部分:结论

我希望这段关于可组合基础设施的简短旅程已经向您展示了将基础设施视为精益制造生产线的好处，并且您可以在 IaaS 和 PaaS 方面两全其美。这里的关键要点是聪明地工作，而不是努力工作。完成工作的心态和过程比你选择使用的工具更有价值。所以放聪明点！