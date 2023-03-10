# 地形条件资源👷如何在 terraform 中制作条件资源？

> 原文：<https://dev.to/tbetous/how-to-make-conditionnal-resources-in-terraform-440n>

我想用这篇短文分享我在 terraform 中关于条件资源的经验。

## 用例🤔

这是我的使用案例。我目前正在为一个项目实验 lambdas (AWS 云功能)。我的问题是不知道哪种编程语言最适合我的用例。这就是为什么我想为每种语言制定一个关于执行时间和内存消耗的基准。

我想要的是创建一个 terraform 配置，它能够通过将参数设置为`"javascript"`或`"java"`来将我的 lambda 从 javascript 切换到 java。

## 解🤯

我的解决方案是使用[元参数](https://www.terraform.io/docs/configuration/resources.html#meta-arguments)。[计数](https://www.terraform.io/docs/configuration/resources.html#count-multiple-resource-instances-by-count)元参数允许指定您想要创建多少个实例。

### 👉步骤 1:创建输入参数

```
variable "lambda_type" {
  type    = "string"
  default = "java"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 👉第二步:创建你的资源

```
locals {
  my_function_name = "awesome-function"
}

resource "aws_lambda_function" "my_lambda_java" {
  filename         = "lambda.jar"
  function_name    = "${local.my_function_name}"
  handler          = "Handler"
  runtime          = "java8"
  count            = "${var.lambda_type == "java" ? 1 : 0}"
}

resource "aws_lambda_function" "my_lambda_javascript" {
  filename         = "lambda.jar"
  function_name    = "${local.my_function_name}"
  handler          = "index.handler"
  runtime          = "nodejs10.x"
  count            = "${var.lambda_type == "javascript" ? 1 : 0}"
} 
```

Enter fullscreen mode Exit fullscreen mode

注意在`lambda_type`值的函数中将`count`设置为 0 或 1 的三进制条件。

### 👉步骤 3:创建一个数据变量，从同一个 id 访问您的条件资源

```
data "aws_lambda_function" "my_lambda" {
  function_name = "${local.my_function_name}"
  depends_on    = ["aws_lambda_function.my_lambda_java", "aws_lambda_function.my_lambda_javascript"]
}

// For instance if your lambda is attached to another resource, 
// you just have to use the same resource id for both (java & javascript)
resource "aws_lambda_permission" "my_lambda_permission" {
  statement_id  = "AllowExecutionFromSNS"
  action        = "lambda:InvokeFunction"
  function_name = "${data.aws_lambda_function.my_lambda.arn}"
  principal     = "sns.amazonaws.com"
  source_arn    = "${aws_sns_topic.topic.arn}"
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`depends_on`属性真的很重要。如果你不使用那个属性，terraform 会尝试获取尚不存在的 lambda 函数。另外，这只适用于 terraform 0.12，它允许[使用`depends_on`和`count = 0`](https://github.com/hashicorp/terraform/issues/15285#issuecomment-447971852) 的资源。

### 👉第四步:计划和应用你的地形

最后，你只需要用合适的参数应用你的地形配置:

```
# For javascript
$ terraform plan --var "lambda-type=javascript"
# If the plan is correct to what you expect :
$ terraform apply --var "lambda-type=javascript"

# For java
$ terraform plan --var "lambda-type=java"
# If the plan is correct to what you expect :
$ terraform apply --var "lambda-type=java" 
```

Enter fullscreen mode Exit fullscreen mode

### 👉步骤 5:清理资源(可选)

如果您按照本文做了一些测试，不要忘记通过破坏资源来清理您的环境！

```
$ terraform destroy 
```

Enter fullscreen mode Exit fullscreen mode

## 恭喜恭喜！🎉

恭喜你！你做到了！现在你可以添加资源条件，从而使你的地形配置更具参数化！

如果你喜欢这篇文章，请不要犹豫:

*   在推特上关注我: [@tbetous](https://twitter.com/tbetous)
*   分享这个帖子！

感谢您的关注和阅读🎉