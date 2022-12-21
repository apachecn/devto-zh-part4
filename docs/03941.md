# 模块中的 Terraform 0.12“为空元组”错误

> 原文：<https://dev.to/piczmar_0/terraform-0-12-is-empty-tuple-error-in-module-17ie>

我正在努力创建一个带有可选资源的模块，这些可选资源由“count”属性控制。

[这里的](https://github.com/piczmar/terraform-sqs-module)是一个演示问题的项目。

有一个 SQS 模块具有可选的死信队列，并附有 SQS 重新驱动策略。

第一次运行时，当用 DLQ 创建队列时，一切正常，但是当我想删除 DLQ 时，问题出现在`terraform plan`运行时:

```
Error: Invalid index

  on modules/sqs/sqs.tf line 67, in resource "aws_sqs_queue" "regular_queue_with_dl":
  67:   redrive_policy             = var.attach_dead_letter_config ? data.template_file.regular_queue_redrive_policy[count.index].rendered : null
    |----------------
    | count.index is 0
    | data.template_file.regular_queue_redrive_policy is empty tuple 
```

有人知道如何在 Terraform 0.12 中用可选资源正确创建这样的模块或解决我面临的问题吗？

* * *

*编辑*:我在 [stackoverflow](https://stackoverflow.com/questions/57908294/optional-resources-in-terraform-0-12-module) 上问了同样的问题，得到了解决我问题的答案。

解决方案是，如果 Lamdba 不应该使用`redrive_policy`时，有条件地将`redrive_policy`分配给`null`，例如:

```
resource "aws_sqs_queue" "regular_queue" {
  redrive_policy = var.attach_dead_letter_config ? templatefile(
    "${path.module}/redrive_policy.tmpl", {
      # (...whatever you had in "vars" in the template_file data resource...)  
    },
  ) : null
...
} 
```

此外，我们可以内联模板文件，而不是创建一个单独的资源。

我更新的例子是[这里](https://github.com/piczmar/terraform-sqs-module/commit/7ad3018b6ba945e0b1d3a459cdc5dcc645959d49)