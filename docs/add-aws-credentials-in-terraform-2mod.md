# 在 terraform 中添加 aws 凭据

> 原文：<https://dev.to/vsreelasya/add-aws-credentials-in-terraform-2mod>

在 terraform 目录中创建一个 terraform.tfvars 文件

如果您按照上面提到的方式命名，terraform 默认将它识别为一个本地环境文件，并且不对它进行版本控制。

编辑文件添加这两个变量

**AWS _ access _ key = " XXXXXXXXXXX "**
**AWS _ secret _ key = " XXXX+XXXX "**

通过这种方式，您不必在每次想要运行时特别提及凭据:

**地形计划**
**地形申请**

如果使用不同的名称创建文件，可以在运行 terraform 命令时传递文件名

上述概念需要记住，AWS 是默认提供者，相关的凭证是默认用户。