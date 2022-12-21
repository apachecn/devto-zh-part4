# 用 Terraform 标记

> 原文：<https://dev.to/david_j_eddy/tagging-with-terraform-5hn3>

[源自我的博客](https://blog.davidjeddy.com/2019/06/25/tagging-with-terraform/)。

使用云资源可以加快业务发展，解放工程团队，但对银行账户来说成本高昂。如果你让一个大型计算或数据库实例在周末运行(或者不小心将 API 密钥提交给 GitHub ),那么这个月的运营成本很容易大幅增加。使用资源元数据**标记**功能，负责方(1)可以审计、跟踪和管理资源。甚至可以根据标签值(或者没有标签值)来自动设定。

## 基本用法

Terraform 中的基本标记结构非常简单。如果您熟悉 JSON 数据交换格式，您应该会立即意识到这一点。这是一个受 Java 启发的对象声明，带有引用的键值对，使用冒号作为分隔符，逗号作为分隔符。是的，另一个要学的 DSL。#welcometowebdev

```
...
"aws_resource_type" "aws_resource" {
    ...
    tags {
        "key": "value",
        "Name": "Value",
        "department": "engineering",
        "team": "core_api",
        "app": "name",
        "env": "dev"
        ...
    }
    ...
}
```

这没什么难的。AWS 中的大多数(不是全部)资源都支持这种方式的标记。简单的 JSON 风格的键/值对。Terraform 甚至允许我们使用变量来代替值，但不允许使用键(2)。

<figure>[![](img/9267dfb680caa3795aed3d284b8263de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WHTnnix9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/Chad_Hagan_Art_and_Design/The-New-Yorker/Gonnorea_final_New_Yorker_f-1440x1440.jpg) 

<figcaption>怪异..</figcaption>

</figure>

```
...
"aws_resource_type" "aws_resource" {
    ...
    tags {
        "key": "value",
        "Name": "Value",
        "department": "${var.dept_name",
        "team": "${var.team_name",
        "app": "${var.app_name",
        "env": "${var.app_env}"
        ...
    }
    ...
}
```

但是 David“你会说”应用程序基础结构可能非常复杂。我需要到处复制/粘贴‘tag’属性吗？”简答；没有。长回答:...

## 高级用法

使用一些 Terraform-fu，我们可以将默认的键/值对集合分配给一个映射类型变量(本地或导入的),并将该变量作为默认的标签数据集。JSON 对象与 Terraform (HCL)地图数据类型之间的格式略有不同；但差不了多少。

```
variable "default_tags" { 
    type = "map" 
    default = { 
        key: "value",
        Name: "Value",
        department: "${var.dept_name",
        team: "${var.team_name",
        app: "${var.app_name",
        env: "${var.app_env}"
  } 
}
...
```

通过将标签抽象为一个映射，资源的标签属性被简化为一行。

<figure>[![](img/52a5dde39a06c877d1c429702be23251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--77CO_OAB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/Chad_Hagan_Art_and_Design/IEEE/IEEE__Complicated_Fin.jpg) 

<figcaption>电路或 IaC 图？你决定！</figcaption>

</figure>

```
...
"aws_resource_type" "aws_resource" {
    ...
    tags = var.default_tags
    ...
}
...
```

更高级的功能是将默认标签映射变量与自定义内联标签合并。为此，我们开始使用 TF merge()。提供默认标记 map 变量作为一个函数参数，自定义标记作为第二个 map 类型函数参数，我们可以使用默认提供的标记和自定义内联标记！嘣，魔法！

```
...
"aws_resource_type" "aws_resource" {
    ...
    tags = "${merge(map( 
            "Special_Key", "some special value", 
            "Special_Key_2", "some other special value",
            ...
        ), var.default_tags)}"
    ...
}
...
```

## 包裹

Hashicorp 不断改进每个版本的 Terraform。说说你对 HCL 接近 JSON 但具有额外功能的看法；它是管理项目基础设施的强大而实用的工具集。通过适当的标记策略，它也成为跟踪基础架构的一种强有力的方法。

## 笔记

*   我是故意说“经理”的。在一个真正的 DevOps 环境中，每一个技术价值流中的每一个团队的每一个开发人员、操作员、系统管理员都应该知道如何对整个系统负责。分担负担。没有筒仓。
*   使用 map()可以将变量用作键。这是因为 map()在返回之前对变量“key”进行了求值。
*   这个概念一开始让我觉得很奇怪。但是在思考了机械之后，这是有意义的。为什么函数中的最后一个操作是在一个没有被返回的变量上？

## 附加阅读

*   [https://weidongzhou . WordPress . com/2018/12/13/how-do-tagging-efficient-in-terra form/](https://weidongzhou.wordpress.com/2018/12/13/how-to-do-tagging-efficiently-in-terraform/)
*   [https://groups.google.com/forum/#!topic/terraform-tool/1yjotodsBog](https://groups.google.com/forum/#!topic/terraform-tool/1yjotodsBog)
*   [https://www . terra form . io/docs/providers/AWS/r/auto scaling _ group . html](https://www.terraform.io/docs/providers/aws/r/autoscaling_group.html)
*   [https://blog . Scott Lowe . org/2018/06/11/using-variables-in-AWS-tags-with-terra form/](https://blog.scottlowe.org/2018/06/11/using-variables-in-aws-tags-with-terraform/)
*   [https://blog . Scott Lowe . org/2018/06/11/using-variables-in-AWS-tags-with-terra form/](https://blog.scottlowe.org/2018/06/11/using-variables-in-aws-tags-with-terraform/)
*   [https://www.terraform.io/docs/configuration/locals.html](https://www.terraform.io/docs/configuration/locals.html)(0.12+)