# 如何在没有强制替换的情况下移动地形状态下的物品

> 原文：<https://dev.to/vsreelasya/how-to-do-move-items-in-terraform-state-without-force-replacement-9e6>

我最近开始研究 terraform，其中一个最烦人的因素是当你不得不改变 terraform 的状态而不强制替换资源时。

Terraform 记住了一种状态，这种状态只不过是基础设施在远程真实世界和本地配置中的样子。因此，当您更改资源中的一些属性时，如资源的名称、实例、已经创建的模块，在计划中它指定为非就地更改

谢天谢地，我的同事提到了一个命令“mv”

这个命令就像一个 Linux mv 命令。

您需要指定源和目标:

**地形状态 mv AWS _ subnet . main AWS _ subnet-us-west-2c**

这将把现有的 terraform 状态移动到更新的 terraform 状态，并且当您运行 terraform 计划时，如果操作正确，您应该不会看到任何基础架构更改。

更多信息可以参考:[https://www.terraform.io/docs/commands/state/mv.html](https://www.terraform.io/docs/commands/state/mv.html)