# Lambda 函数、SLS 和 Node 入门

> 原文：<https://dev.to/mlevkov/managing-and-deploying-aws-lambda-functions-with-sls-4ofh>

在工作中，我的团队有一个有趣的任务，每天处理上传到 S3 桶中的某些文件。我们决定试用 AWS Lambda，而不是部署一个应用程序并永远在后台运行。

这篇文章将从头到尾概述我们如何设置 Lambda。我将回顾我们如何使用 SLS 管理云结构堆栈，探索针对不同环境设置它的方法，并最后回顾它的部署。

首先，你需要一些东西:

1.  AWS 帐户
2.  机器上安装的节点 v8.x(至少)
3.  机器上安装的 AWS CLI(命令行界面)
4.  安装在您机器上的 SLS CLI

为了成功管理我们的 Lambda 部署，我决定使用[无服务器库](https://serverless.com/)。这个库非常强大，允许我们用 YAML 文件管理我们的整个堆栈。

在我们的用例中，我们希望创建一个 S3 桶，在收到文件时触发 Lambda 函数中的特定方法。所有这些都在 YAML 文件的几行中进行了定义。让我们来看看。

首先，在我们的项目中，我们添加了一个带有以下参数的 serverless.yml 文件: