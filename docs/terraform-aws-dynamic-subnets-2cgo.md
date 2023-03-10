# Terraform AWS -动态子网

> 原文：<https://dev.to/prodopsio/terraform-aws-dynamic-subnets-2cgo>

**更新**-[2019 年 10 月 17 日变更日志](https://github.com/hashicorp/terraform/blob/v0.12.11/CHANGELOG.md#01211-october-17-2019) : Terraform 发布了一个名为 [cidrsubnets](https://www.terraform.io/docs/configuration/functions/cidrsubnets.html) 的新函数，这个函数创建了一个 cidr-subnets 列表。这个功能很棒，推荐使用。即使这个函数缩短了本教程的某些部分，如果你想学习如何在 Terraform 中使用函数，你仍然应该阅读它。

# 目标

1.  动态创建子网集
2.  学习 Terraform 中的高级概念
    *   映射变量和查找函数
    *   for 循环和条件 for 循环
    *   for 循环中的索引函数

# 知识和假设

1.  您对子网划分(a.b.c.d/xx)很熟悉，这是一个很好的计算子网的在线工具
2.  您使用的是 Terraform v0.12+
3.  您以前有以下经验:
    *   [变量](https://www.terraform.io/docs/configuration/variables.html)
    *   [本地值](https://www.terraform.io/docs/configuration/locals.html)
    *   [模块](https://www.terraform.io/docs/configuration/modules.html)
4.  你知道循环的[是什么吗](https://docs.python.org/3/tutorial/controlflow.html#for-statements)
5.  我将为 vpc 使用模块[terra form-AWS-modules/VPC/AWS](https://github.com/terraform-aws-modules/terraform-aws-vpc)。此模块需要子网列表(私有、数据库、公共)，这正是我们将要创建的内容

# 问题#1:每个环境的子网

对每个环境的子网进行硬编码会带来很大的开销。这样做的话，您可能会写下很多带有硬编码网络前缀的代码行。此外，它取消了基础设施即代码(IaC)必须提供的灵活性。

## **按环境设置** cidr_ab

cidr_ab 来救援！将 VPC CIDR 的起点设置为映射变量，并将每个 cidr_ab 值映射到适当的环境。如果您没有使用所有四个，请将其从映射中移除。

子网模式(前缀)是: **a.b** .c.d/xx，因此这将覆盖每个环境中所有子网的 **a.b** 部分。比如:

```
variable "cidr_ab" {
    type = map
    default = {
        development     = "172.22"
        qa              = "172.24"
        staging         = "172.26"
        production      = "172.28"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## **按环境获取** cidr_ab

地图和查找函数提供了强大的功能，在我们的例子中，它可以很容易地获得每个环境的 cidr_ab。

下面是[查找函数](https://www.terraform.io/docs/configuration/functions/lookup.html) : `lookup(map, key, default)`的语法

要为每个环境创建一个包含相关 cidr_ab 的本地列表`private_subnets`，请使用以下命令:

*提醒*:连接表情和文字:“${expression}我的文字”

```
locals {
    private_subnets = [
        "${lookup(var.cidr_ab, var.environment)}.1.0/24",
        "${lookup(var.cidr_ab, var.environment)}.2.0/24",
        "${lookup(var.cidr_ab, var.environment)}.3.0/24"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，我们也需要对数据库和公共子网这样做。

## 问题#1:完整解决方案

假设我们想要创建以下子网:私有、数据库和公共。下面是我们的`variables.tf`和`vpc.tf`文件的样子:

`variables.tf`

```
variable "cidr_ab" {
    type = map
    default = {
        development     = "172.22"
        qa              = "172.24"
        staging         = "172.26"
        production      = "172.28"
    }
}

locals {
    private_subnets         = [
        "${lookup(var.cidr_ab, var.environment)}.1.0/24",
        "${lookup(var.cidr_ab, var.environment)}.2.0/24",
        "${lookup(var.cidr_ab, var.environment)}.3.0/24"
    ]

    database_subnets        = [
        "${lookup(var.cidr_ab, var.environment)}.11.0/24",
        "${lookup(var.cidr_ab, var.environment)}.12.0/24",
        "${lookup(var.cidr_ab, var.environment)}.13.0/24"
    ]

    public_subnets          = [
        "${lookup(var.cidr_ab, var.environment)}.64.0/24",
        "${lookup(var.cidr_ab, var.environment)}.65.0/24",
        "${lookup(var.cidr_ab, var.environment)}.66.0/24"
    ]
}

variable "environment" {
    type = string
    description = "Options: development, qa, staging, production"
} 
```

Enter fullscreen mode Exit fullscreen mode

`vpc.tf`

```
module "vpc" {
    source = "terraform-aws-modules/vpc/aws"
    version = "~>2.0"
    name                 = "my-vpc"
    cidr                 = "${lookup(var.cidr_ab, var.environment)}.0.0/16"
    private_subnets      = local.private_subnets
    database_subnets     = local.database_subnets
    public_subnets       = local.public_subnets

    azs                  = ["us-west-2a", "us-west-2b", "us-west-2c"]

    # omitted arguments for brevity

} 
```

Enter fullscreen mode Exit fullscreen mode

# 问题#2:每个可用性分区的子网

在问题#1 中，我们解决了每个环境的子网，但是可用性区域(az)是硬编码的。

有人可能会想；让我们简单地为该区域创建一个 map 变量，然后使用该区域的名称，后跟“a”、“b”、“c”。例如:

```
variable "region" {
    type = map(string)
    default = {
        "development" = "us-west-2"
        "qa"          = "us-east-2"
        "staging"     = "us-east-1"
        "production"  = "ca-central-1"
    }
}

module "vpc" {

    # omitted arguments for brevity

    azs = [
        "${lookup(var.region, var.environment)}a",
        "${lookup(var.region, var.environment)}b",
        "${lookup(var.region, var.environment)}c",
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在使用的所有区域都有相同数量的可用区域，那么上述解决方案是完美的。不幸的是，对于只有两个可用区域的 **ca-central-1** (加拿大中央)来说，情况并非如此。另一个例子是 **us-east-1** (弗吉尼亚州)，它有六个可用区。

*注*:使用 [AWS 全球基础设施](https://aws.amazon.com/about-aws/global-infrastructure/)找出每个地区的可用区域数量。以及[地区和可用区域](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)以计算出该地区的代码(例如 eu-west-1)

## 数据 aws _ 可用性 _ 区域

通过使用[数据“avail ability _ zones”](https://www.terraform.io/docs/providers/aws/d/availability_zones.html)，我们可以得到当前发生的区域的可用性区域。

我们是这样做的:

```
data "aws_availability_zones" "available" {
    state = "available"
} 
```

Enter fullscreen mode Exit fullscreen mode

关于上面代码的一些解释:

1.  `data` -获取您帐户中现有的数据/资源
2.  `aws_availability_zones` -获取当前区域的可用区域列表
3.  `available` -数据的名称，选择一个反映数据含义的名称很重要
4.  `state = "available"` -过滤掉当前经历中断的可用性区域

让我们将数据保存在一个本地值中。你可能想知道，“为什么我们使用本地值？我们对子网也是这样做的，为什么呢？”

经验法则——如果你有机会操作一个变量/值，使用一个局部值。这个技巧提供了管理`local.var_name`的粒度。最好在后台管理变量，不要接触基础设施的代码(vpc.tf，rds.tf，s3.tf 文件)。最坏的情况——我们使用了一个本地值，尽管我们没有操纵它。

在本地值
中保存可用区域[名称](https://www.terraform.io/docs/providers/aws/d/availability_zones.html#attributes-reference)列表

```
locals {
    availability_zones = data.aws_availability_zones.available.names
} 
```

Enter fullscreen mode Exit fullscreen mode

# 问题#2:完整解决方案

`variables.tf`

```
variable "cidr_ab" {
    type = map
    default = {
        development     = "172.22"
        qa              = "172.24"
        staging         = "172.26"
        production      = "172.28"
    }
}

locals {
    private_subnets         = [
        "${lookup(var.cidr_ab, var.environment)}.1.0/24",
        "${lookup(var.cidr_ab, var.environment)}.2.0/24",
        "${lookup(var.cidr_ab, var.environment)}.3.0/24"
    ]

    database_subnets        = [
        "${lookup(var.cidr_ab, var.environment)}.11.0/24",
        "${lookup(var.cidr_ab, var.environment)}.12.0/24",
        "${lookup(var.cidr_ab, var.environment)}.13.0/24"
    ]

    public_subnets          = [
        "${lookup(var.cidr_ab, var.environment)}.64.0/24",
        "${lookup(var.cidr_ab, var.environment)}.65.0/24",
        "${lookup(var.cidr_ab, var.environment)}.66.0/24"
    ]
}

data "aws_availability_zones" "available" {
    state = "available"
}

locals {
    availability_zones = data.aws_availability_zones.available.names
}

variable "environment" {
    type = string
    description = "Options: development, qa, staging, production"
} 
```

Enter fullscreen mode Exit fullscreen mode

`vpc.tf`

```
module "vpc" {
   source = "terraform-aws-modules/vpc/aws"
    version = "~>2.0"
    name                 = "my-vpc"
    cidr                 = "${lookup(var.cidr_ab, var.environment)}.0.0/16"
    private_subnets      = local.private_subnets
    database_subnets     = local.database_subnets
    public_subnets       = local.public_subnets

    azs                  = local.availability_zones

    # omitted arguments for brevity

} 
```

Enter fullscreen mode Exit fullscreen mode

# 第三期:动态 cidr_c

这就是我贪婪的地方，我想根据可用区域的数量填充 **cidr_c** 。到目前为止，我们已经讲述了 **a.b** .c.d/xx，现在我们将讲述这部分 a.b. **c** 。d/xx

例如(伪代码):

```
# define cidr_c per subnet
cidr_c_private_subnets =  1
cidr_c_database_subnets = 11
cidr_c_public_subnets = 64

number_of_azs = 2

# dynamically populate lists of subnets
private_subnets  = ["a.b.1.d/xx", "a.b.2.d/xx"]
database_subnets = ["a.b.11.d/xx", "a.b.12.d/xx"]
public_subnets   = ["a.b.64.d/xx", "a.b.65.d/xx"] 
```

Enter fullscreen mode Exit fullscreen mode

## 为循环

动态填充听起来像一个循环，在 Terraform 中，我们得到了循环的[。](https://www.terraform.io/docs/configuration/expressions.html#for-expressions)

Terraform 的 for 循环让我想起了 [Python 的 list comprehension](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions) ，意思是——用 for 循环创建一个新列表。

for 循环语句的语法:
`[for item in list: do_something_on(item)]`

我们需要的循环伪代码:

```
for availability_zone in local.availability_zones:
  "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_private_subnets + index_of_availability_zone}.0/24" 
```

Enter fullscreen mode Exit fullscreen mode

关于上面代码的一些解释:

1.  `availability_zone` -我选择了迭代器的名称，它也可以是`az`或任何其他名称
2.  `index_of_availability_zone`
    *   列表的第一个索引为零(0)
    *   假设`${lookup(var.cidr_ab, var.environment)`对于私有子网等于一(1)
    *   将可用性区域的当前索引添加到`${lookup(var.cidr_ab, var.environment)`，导致:
        *   1 + 0
        *   1 + 1
        *   1 + 2

## 获取可用区的索引

[指标函数](https://www.terraform.io/docs/configuration/functions/index.html)来救援了！

索引函数语法:
`index(list_to_search_in, value_to_search_for)`

我们正在搜索的列表是`local.availability_zones`，我们正在寻找的值是循环的迭代器，在我们的例子中是`availability_zone`。

用`index(local.availability_zones, az)`替换`index_of_availability_zone`的结果是:

```
locals {
    private_subnets = [
        for az in local.availability_zones:
          "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_private_subnets + index(local.availability_zones, az)}.0/24"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 问题#3:完整解决方案

每个循环中迭代器的名字是`az`，我选择了一个比`availability_zone`更短的名字，以便于阅读。因为我们使用本地值，我们只需要改变`variables.tf`文件，不需要接触`vpc.tf`文件

`variables.tf`

```
variable "cidr_ab" {
    type = map
    default = {
        development     = "172.22"
        qa              = "172.24"
        staging         = "172.26"
        production      = "172.28"
    }
}

locals {
    cidr_c_private_subnets  = 1
    cidr_c_database_subnets = 11
    cidr_c_public_subnets   = 64
}

data "aws_availability_zones" "available" {
    state = "available"
}

locals {
    availability_zones = data.aws_availability_zones.available.names
}

variable "environment" {
    type = string
    description = "Options: development, qa, staging, production"
}

locals {
    private_subnets = [
        for az in local.availability_zones : 
            "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_private_subnets + index(local.availability_zones, az)}.0/24"
        ]
    database_subnets = [
        for az in local.availability_zones : 
            "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_database_subnets + index(local.availability_zones, az)}.0/24"
        ]
    public_subnets = [
        for az in local.availability_zones : 
            "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_public_subnets + index(local.availability_zones, az)}.0/24"
        ]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 问题#4:限制子网数量

在解决方案#3 中，我们根据可用性区域的数量来填充子网，这非常好，但在使用模块[terra form-AWS-modules/VPC/AWS](https://github.com/terraform-aws-modules/terraform-aws-vpc)时，这可能会导致不必要的行为。

如果您希望每个可用性分区都有一组子网，而不关心每个区域创建了多少个子网，您可以就此打住。
用一个例子来解释会更容易些:
ca-central-1(加拿大中部)- 2 个可用区域，因此有 **6 个**子网
us-west-2(俄勒冈州)- 4 个可用区域，因此有 **12 个**子网

如果您希望所有环境(和区域)中的子网数量相似，请继续阅读。例如:
假设我们必须使用 ca-central-1(加拿大中心)，正因为如此，我们被迫在每个地区只使用两个可用性区域。
ca-Central-1(Canada Central)-2 个可用区域，因此 **6 个**子网
us-west-2 (Oregon) - 4 个可用区域，强制最大数量的 **6 个**子网(每种子网 2 个)
总共，我们的 VPC 将有 6 个子网(不包括 VPC 附带的“默认”子网)。

经验法则——我们需要在我们打算使用的所有区域中可用的最低通用数量的可用性区域。

## 子网的最大数量

我们现在将为每个子网初始化另外三个变量`subnet_max`。

`variables.tf`

```
locals {
    cidr_c_private_subnets  = 1
    cidr_c_database_subnets = 11
    cidr_c_public_subnets   = 64

    max_private_subnets     = 2
    max_database_subnets    = 2
    max_public_subnets      = 2
} 
```

Enter fullscreen mode Exit fullscreen mode

## 条件为循环

幸运的是 for 循环有一个内置的关键字 [if](https://www.terraform.io/docs/configuration/expressions.html#for-expressions) ，这仍然让我想起 Python 的列表理解:)

带条件的 for 循环的语法:
`[for item in list: do_something_on(item) if expression]`

我们的目标是遍历可用性区域，直到达到所需的最大子网数量。

# 问题#4:完整解决方案

当然，我们只需要修改`variables.tf`文件。

`variables.tf`

```
variable "cidr_ab" {
    type = map
    default = {
        development     = "172.22"
        qa              = "172.24"
        staging         = "172.26"
        production      = "172.28"
    }
}

locals {
    cidr_c_private_subnets  = 1
    cidr_c_database_subnets = 11
    cidr_c_public_subnets   = 64

    max_private_subnets     = 2
    max_database_subnets    = 2
    max_public_subnets      = 2
}

data "aws_availability_zones" "available" {
    state = "available"
}

locals {
    availability_zones = data.aws_availability_zones.available.names
}

variable "environment" {
    type = string
    description = "Options: development, qa, staging, production"
}

locals {
    private_subnets = [
        for az in local.availability_zones : 
            "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_private_subnets + index(local.availability_zones, az)}.0/24"
            if index(local.availability_zones, az) < local.max_private_subnets
        ]
    database_subnets = [
        for az in local.availability_zones : 
            "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_database_subnets + index(local.availability_zones, az)}.0/24"
            if index(local.availability_zones, az) < local.max_database_subnets
        ]
    public_subnets = [
        for az in local.availability_zones : 
            "${lookup(var.cidr_ab, var.environment)}.${local.cidr_c_public_subnets + index(local.availability_zones, az)}.0/24"
            if index(local.availability_zones, az) < local.max_public_subnets
        ]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

1.  一些解决方案架构师可能更喜欢在`vpc.tf`文件中硬编码子网前缀，这在大多数情况下会很好。事实上，我每次只需要修改文件`variables .tf`是无价的
2.  `variables.tf`文件中变量顺序背后的逻辑-
    *   应该修改的变量，例如:
        *   每个环境的区域(地图)
        *   每种子网类型的本地 cidr_c 和最大子网
    *   静态变量/数据，例如:
        *   数据“aws_availability_zones”“可用”
        *   local .可用性 _ 区域
        *   每种子网类型的 local.subnet_list
3.  我发现使用本地值代替变量是一个好方法，它提供了我在设计基础设施时所需要的灵活性
    *   我们需要变量的唯一情况是当我们想要提示输入值时，例如:`var.environment`
    *   始终使用本地值；与变量不同，它们提供了使用函数的能力
    *   我使用了`var.environment`和`var.cidr_ab`——但是我应该把它们赋给本地值。我使用了变量，因为我想使本教程通用。记住-本地值是最好的
4.  AWS 一直在添加可用性区域(az ),所以示例中每个区域的可用性区域数量可能已经过时了。请务必检查 az 的当前数量
    *   [AWS 全球基础设施](https://aws.amazon.com/about-aws/global-infrastructure/)
    *   [地区和可用区](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

你喜欢这个教程吗？拍手/心形/独角兽，分享给你的朋友同事。你不喜欢吗？让我知道是哪些部分，我会处理的。

我的下一篇文章将是关于 [Terraform 云](https://app.terraform.io/session)和 [Terraform 的工作空间](https://www.terraform.io/docs/state/workspaces.html)。

* * *

最初发布于 [https://www.prodops.io](https://www.prodops.io/blog/terraform-aws-dynamic-subnets) 。