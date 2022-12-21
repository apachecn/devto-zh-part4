# 使用 Terraform 表达式和函数构建动态输出

> 原文：<https://dev.to/sophiedebenedetto/building-dynamic-outputs-with-terraform-expressions-and-functions-2g1b>

我们知道我们可以定义一个 Terraform 模块，它为*另一个*模块产生输出作为输入。但是我们如何从一个创建了*集合*资源的模块中构建动态输出，并将该输出*格式化为恰好*作为其他地方的输入呢？在 Terraform `for`和`zipmap`函数的帮助下，这是可能的。继续阅读，了解更多！

## 静态接近

在熨斗学校构建一组 Terraform 模块来定义一些 AWS 资源时，我们遇到了一个有趣的挑战。我们需要能够做到以下几点:

*   定义一组 AWS Secrets Manager 资源。
*   使用这些秘密创建一个带有容器定义的 AWS ECS 任务定义资源。

解决这个问题的静态方法应该是这样的。

**步骤 1:创建 AWS Secrets Manager 资源**

```
resource "aws_secretsmanager_secret" "my-app-PGUSER" {
  name                    = "my-app-PGUSER"
  recovery_window_in_days = "30"
}

resource "aws_secretsmanager_secret" "my-app-PGPASSWORD" {
  name                    = "my-app-PGPASSWORD"
  recovery_window_in_days = "30"
} 
```

**第二步:创建容器定义**

```
resource "aws_ecs_task_definition" "app-task-definition" {
  ...

  container_definitions = <<DEFINITION
[
  {
    ...
    "secrets": [{
        "name": "PGUSER",
        "valueFrom": "${aws_secretsmanager_secret.my-app-PGUSER.arn}"
      }, {
        "name": "PGPASSWORD",
        "valueFrom": "${aws_secretsmanager_secret.my-app-PGPASSWORD.arn}"
      }
]
DEFINITION
} 
```

虽然这种方法可行，但它有几个明显的缺点。

**静态，而不是动态**

在本例中，我们只建立了两个 AWS Secrets Manager 秘密，`my-app-PGUSER`和`my-app-PGPASSWORD`，并将这些秘密的值作为环境变量传递到容器定义中。当我们的应用程序有更多的秘密时会发生什么？必须手动写出每个秘密的资源定义，并相应地更新容器定义，这导致了大量重复和冗长的 Terraform 代码。读起来很难，写起来很烦。我们讨厌它，有一个更好的方法！

**具体，而不是抽象**

当我们接下来需要创建一组资源和一个容器定义时——可能是为一个全新的应用程序——会发生什么？我们将不得不重新编写所有这些代码！我们也讨厌这样，希望有更好的方法！

通过创建一组可重用的模块，我们可以很容易地在将来添加更多的秘密作为 env 变量*和*我们可以很容易地动态创建带有适当秘密的全新任务定义。

## 用`for_each`构建动态资源

首先，我们将解决我们的重复问题。我们将定义一个可重用的模块，接受要建立的秘密列表的输入，而不是为我们想要创建的每个秘密手动编写一个新的 AWS Secrets Manager 资源定义。我们的模块将使用 Terraform 的 [`for_each`](https://www.terraform.io/docs/configuration/expressions.html) 表达式来遍历列表，并为每个列表创建一个资源。

我们想定义一个模块，它用两个输入来调用:

*   应用程序机密列表，我们将把它作为`application_secrets`输入传入。
*   应用程序的名称。

这样，我们可以创建一组 AWS Secrets Manager 资源，命名约定为:`my-app-THE_SECRET`。

我们将在子目录`terraform/prod-us-east-1/secrets-modules/`中定义我们的模块，我们将从`main.tf`定义开始。

```
# terraform/prod-us-east-1/secrets-module/main.tf

resource "aws_secretsmanager_secret" "application-secret" {
  for_each                = toset(var.application_secrets)
  name                    = "${var.name}-${each.value}"
  recovery_window_in_days = "30"
} 
```

这里，我们在资源定义中使用 Terraform 的`for_each`表达式。这样做的效果是迭代我们传递给模块调用的秘密列表，并为每个秘密创建一个资源。

注意，我们在`var.application_secrets`上使用了 [`toset`](https://www.terraform.io/docs/configuration/functions/toset.html) 功能。这是因为`for_each`只对`set`数据类型进行操作，但是我们的应用程序秘密作为一个列表被传递到模块调用中。

还要注意，我们的资源定义不会起作用，除非我们定义了它想要使用的变量:

```
# terraform/prod-us-east-1/secrets-module/variables.tf

variable "application_secrets" {
  description = "list of application secret names"
  type        = "list"
  default     = []
}

variable "name" {
  description = "the name of your application to prepend to secret names in AWS Secrets Manager"
  type        = "string"
  default     = "application name"
} 
```

现在让我们看看如何调用这个模块来定义一组特定的应用程序秘密。

```
# terraform/prod-us-east-1/my-app-secrets.tf

variable "my-app-application-secrets" {
  type = list(string)
  default = [
    "PGPASSWORD",
    "PGUSER"
  ]
}

module "my-app-application-secrets" {
  source              = "./secrets-module"
  name                = "my-app"
  application_secrets = var.my-app-application-secrets
} 
```

将应用程序秘密列表定义为变量`"my-app-application-secrets"`，我们使用我们的`secrets-module`作为源创建一个模块。我们称这个模块有两个输入:`name`和`my-app-application-secrets`列表。

现在，如果我们运行`terraform plan`，我们将看到下面的资源将被创建:

```
 # module.my-app-application-secrets.aws_secretsmanager_secret.application-secret["PGPASSWORD"] will be created
 + resource "aws_secretsmanager_secret" "application-secret" {
     + arn                     = (known after apply)
     + id                      = (known after apply)
     + name                    = "my-app-PGPASSWORD"
     + name_prefix             = (known after apply)
     + recovery_window_in_days = 30
     + rotation_enabled        = (known after apply)
   }

 # module.my-app-application-secrets.aws_secretsmanager_secret.application-secret["PGUSER"] will be created
 + resource "aws_secretsmanager_secret" "application-secret" {
     + arn                     = (known after apply)
     + id                      = (known after apply)
     + name                    = "my-app-PGUSER"
     + name_prefix             = (known after apply)
     + recovery_window_in_days = 30
     + rotation_enabled        = (known after apply)
   } 
```

这种方法为我们赢得了两件事:

*   现在，当我们想要创建一个新的 AWS Secrets Manager 资源时，我们需要做的就是添加到在`my-app-appication-secrets`变量中定义的列表中。这比编写一个全新的资源定义要容易得多！
*   当我们想要为一个*新的*应用程序构建一组 AWS Secrets Manager 资源时，我们可以轻松地调用我们的`secrets-module`模块。我们需要做的就是定义一个新的模块`my-other-app-application-secrets`，用它自己的变量列出要创建的秘密。轻松点。

虽然我们已经解决了定义秘密时的重复问题，但是我们仍然需要手动获取每个秘密资源的 ARNs，以便为我们的容器定义构造输入。

让我们通过教我们的 secrets 模块生成可以传递到容器定义中的输出来解决这个问题。

## 用`for`构建动态资源

我们需要我们的 secrets 模块来输出容器定义所需的*确切的*输入。在我们讨论如何做到这一点之前，让我介绍一下我们的设置所使用的容器定义模块。这篇文章假设我们已经设置了一个容器定义模块，它需要以下输入:

```
module "my-app-container-definition" {
  source          = "./container-modules"
  ...
  secrets = [
    {
      name = "PGUSER",
      valueFrom = ${aws_secretsmanager_secret.my-app-PGUSER.arn}
    },
    {
      name = "PGPASSWORD",
      valueFrom = ${aws_secretsmanager_secret.my-app-PGPASSWORD.arn}
    }
  ]
} 
```

这里我们不会关注这个特定的模块是如何定义的。如果您想更深入地研究容器定义模块，请查看启发我们的源代码[这里](https://github.com/cloudposse/terraform-aws-ecs-container-definition)。

因此，我们需要我们的 secrets 模块来动态地生成看起来*完全*像这样的输出:

```
[
  {
    name = "PGUSER",
    valueFrom = <arn of this secret resource>,
  {
    name = "PGPASSWORD",
    valueFrom = <arn of this secret resource>,
] 
```

依此类推，我们通过`application_secrets`输入将每个秘密输入到秘密模块中。我们开始吧！

## [T3 用`for`和`zipmap`产生动态输出](#generating-dynamic-output-with-raw-for-endraw-and-raw-zipmap-endraw-)

我们将为我们的秘密模块定义一个输出:

```
# terraform/prod-us-east-1/secrets-module/output.tf

output "application-secret-mappings" {
  value = # coming soon!
} 
```

我们的输出需要遍历通过`application_secrets`输入传递给模块的所有秘密，将每个秘密映射到其对应的 ARN，并构建一个有效载荷列表:

```
{
  name = < secret name>,
  valueFrom = < secret ARN>
} 
```

现在，秘密名字和 ARNs 作为两个独立的列表存在。我们的秘密名字在这里:

```
var.application_secrets 
```

我们的 ARN 列表可以像这样在模块中查找:

```
values(aws_secretsmanager_secret.application-secret)[*]["arn"] 
```

我们将使用 Terraform 的 [`zipmap`](https://www.terraform.io/docs/configuration/functions/zipmap.html) 函数来构建一个映射，其中键是秘密名称，值是相应的 ARNs:

```
zipmap(
  sort(var.application_secrets),
  sort(values(aws_secretsmanager_secret.application-secret)[*]["arn"])
) 
```

*注意，我们使用`sort`函数来确保正确的秘密被映射到正确的 ARN* 。

这将产生下面的地图:

```
{
  "PGUSER" = aws_secretsmanager_secret.application-secret["PGUSER"].arn,
  "PGPASSWORD" = aws_secretsmanager_secret.application-secret["PGPASSWORD"].arn
} 
```

现在，借助于 [`for`](https://www.terraform.io/docs/configuration/expressions.html#for-expressions) 表达式，我们可以在输出定义中迭代这个映射，以便收集一个针对我们的容器定义正确格式化的对象列表。

```
# where `secrets_map` is the result of our zipmap call
[ for secret, arn in secrets_map : map("name", secret, "valueFrom", arn) ] 
```

将所有这些放在我们的输出定义中:

```
output "application-secret-mappings" {
  value = [
    for secret, arn in zipmap(
      sort(var.application_secrets),
      sort(values(aws_secretsmanager_secret.application-secret)[*]["arn"])) :
      map("name", secret, "valueFrom", arn)
  ]
} 
```

这将产生以下输出:

```
[
  {
    name = "PGUSER",
    valueFrom = <arn of this secret resource>,
  {
    name = "PGPASSWORD",
    valueFrom = <arn of this secret resource>,
] 
```

这正是我们的容器定义所需要的！

在容器定义输入中调用这个输出之前的最后一步。我们需要告诉`my-app-secrets`模块产生它自己的输出，使用`"application-secret-mappings"`输出作为源。

回到我们的`my-app-secrets.tf`文件:

```
# terraform/prod-us-east/my-app-secrets.tf

output "my-app-application-secrets" {
  description = "List of secrets mapped to ARNs"
  value       = module.my-app-application-secrets.application-secret-mappings
} 
```

现在，当我们调用容器定义模块时，我们就可以在输出中使用它了！

## 在容器定义中使用动态输出

记住，我们有一个容器定义模块，看起来像这样:

```
module "my-app-container-definition" {
  source          = "./container-modules"
  ...
  secrets = [
    {
      name = "PGUSER",
      valueFrom = ${aws_secretsmanager_secret.my-app-PGUSER.arn}
    },
    {
      name = "PGPASSWORD",
      valueFrom = ${aws_secretsmanager_secret.my-app-PGPASSWORD.arn}
    },
  ]
} 
```

我们希望用我们的动态秘密模块输出替换静态秘密列表。我们可以这样做:

```
module "my-app-container-definition" {
  source  = "./container-modules"
  secrets = module.my-app-application-secrets.application-secret-mappings
  ...
} 
```

就是这样！这种方法为我们赢得了一些东西:

*   当我们需要向给定应用的容器中添加新的秘密时，我们*不需要*手动创建 AWS 秘密管理器资源定义，*和*捕获 ARN *和*更新容器定义。我们*只需要*将新的秘密添加到`my-app-application-secrets`变量中的应用程序秘密列表中，并应用我们的更改。这将创建资源并更新输出，从而更新容器定义以包括新的秘密和免费的 ARN！
*   我们已经把构建新的应用程序及其资源变成了一个相对快速和轻松的过程。我们可以一次又一次地调用我们的 secrets 模块，为我们想要启动的任何新应用程序动态地创建 secrets 资源，并在每个应用程序的相应容器定义中轻松地使用这些模块调用的输出。

## 结论

Terraform 的可重用模块和有用的表达式和函数允许我们编写枯燥的“基础设施代码”。在`for_each`表达式的帮助下，我们能够定义一个模块，用几行代码动态创建 AWS Secrets Manger 资源。在`for`表达式和`zipmap`函数的帮助下，我们能够将该模块向前推进一步。我们为相应的容器定义输入定义了格式*恰到好处*的动态模块输出。结果，我们给了自己一个合理的、可复制的方法来生成复杂的应用程序基础设施。