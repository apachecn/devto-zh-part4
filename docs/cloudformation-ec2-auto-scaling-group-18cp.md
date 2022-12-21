# 在 CloudFormation 中创建的 EC2 Auto Scaling Group 堆栈不会被删除吗？

> 原文：<https://dev.to/shootacean/cloudformation-ec2-auto-scaling-group-18cp>

记述作为 CloudFormation 堆栈创建的 EC2 Auto Scaling Group 没有被删除时进行的调查。

在 CFn(CloudFormation )中创建 EC2 Auto Scaling Group 的堆栈，进行缩放验证后，

即使删除创建的堆栈，也会出现 EC2 Auto Scaling Group 和 EC2 实例

## Conclusion

什么都不做等 10 分钟左右就被删除了。

## 已确认

*   删除堆栈操作后，即使在管理控制台上确认 EC2 Auto Scaling Group 也不会显示`削除中`
*   维持实例数、最小实例数、最大实例数为`0`。
*   与 EC2 Auto Scaling Group 关联的 EC2 实例的启动状态保持`動作中`
*   手动删除 EC2 Auto Scaling Group 和 EC2 实例也会删除堆栈

## 调查的事情

官方文档的《[自动缩放组的删除](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/delete-auto-scaling-group.html)》中记载如下。

> Scalingグループを汽车削除する前にすべてのインスタンスを終了するには、UpdateAutoScalingGroupを呼び出して、Auto Scalingグループの最小サイズと必要な容量をゼロに設定します。
> 要在删除自动缩放组之前终止所有实例，请调用 UpdateAutoScalingGroup 并将自动缩放组的最小大小和所需容量设置为零。

此次 CFn 堆栈删除中的 AutoScalingGroup 删除似乎采取了上述方法。

在通过 CFn 进行构成管理之前，

1.  从管理控制台创建自动缩放组
2.  Syndrome
3.  从管理控制台中删除 AutoScalingGroup
4.  确认关联的 EC2 实例将被删除

这样的过程，AutoScalingGroup 删除操作几秒钟后就开始删除 EC2 实例了，所以

我以为删除 AutoScalingGroup 的同时，EC2 实例也会被删除

## 课题和不知道的事情

*   要使用的服务的文档可以是粗略的，所以应该读一遍。
*   根据设定的不同，行为也不同，需要记住每个使用的服务的默认行为都不同。

## Reference

*   [删除自动缩放组](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/delete-auto-scaling-group.html)
*   [CloudFormation 中的 AutoScalingGroup 下属的实例交换方法](https://qiita.com/ryo0301/items/f0c2d155874b14427dc6)
*   [AWS AutoScallingGroup 的删除失败](https://www.ted027.com/post/aws-autoscalling-suspend/)
*   [将 AWS CloudFormation 构成的 AutoScaling 实例像公式年迁宫一样进行替换的方法 2](https://dev.classmethod.jp/cloud/two-ways-replacing-update-on-cloudformation/)