# 无服务器框架:Gerando artefatos para deploy em CI/CD

> 原文：<https://dev.to/oieduardorabelo/serverless-framework-gerando-artefatos-para-deploy-em-ci-cd-3i1j>

在本文中，我们将了解如何生成工件，以便在您的 CI / CD 管道中部署到您的应用程序**【server less framework】**。

在考虑部署或回滚无服务器应用程序时，必须将每个服务视为单独的服务。这意味着您希望分别为每个服务创建和管理部署对象。并且您希望控制回滚和部署过程。

在讨论如何为无服务器框架应用程序创建部署对象之前，让我们先讨论一下命令“`serverless deploy`”在内部的工作方式。

# 科莫`serverless deploy` funciona

运行`serverless deploy`时，后台运行两个步骤。Serverless Framework 首先运行“`serverless package`”以打包代码并创建云信息模型。默认情况下，对象在项目根目录的“`.serverless/`”目录中生成，其内容如下:

*   一个 zip 文件，其中包含您的服务的 Lambda 代码。或者，如果启用了“T1”，则为您服务中的每个 lambda 函数创建 zip 文件。
*   描述在`serverless.yml`中定义的功能的 cloudformation 模板文件。
*   Serverless Framework 在内部使用的文件`serverless-state.json`。

生成对象后，Serverless 将部署此软件包:

1.  正在将 zip 文件上载到存储桶 S3。
2.  更新 cloud 信息模型并将存储桶 S3 的路径添加到 lambda zip 文件中。
3.  将模板发送到 cloudtraining，以便在 AWS 中开始实施。

# 产生部署人工因素

对于我们的 CI / CD 管道，我们希望将此过程分为两个步骤。为此，我们先做包装:

```
$ serverless package --package PACKAGE_DIR 
```

这会在`PACKAGE_DIR`目录中生成软件包

然后我们可以存储这个包以便以后重用。用与此包关联的 git commit 标记此包是一个好主意。或指定其他唯一 ID，以便稍后用于查找此对象。

当我们准备好部署此软件包或尝试回滚到此提交时，我们将引用此软件包并使用:
启动部署

```
$ serverless deploy --package PACKAGE_DIR 
```

`PACKAGE_DIR`是生成部署对象的位置。

如果用`--package`、**选项运行`serverless deploy`命令，则跳过封装步骤**，使用您提供的工件。

这提供了对 Serverless Framework 生成的部署对象的控制，使其能够更好地集成到您的 CI / CD 管道中。

* * *

# 克雷蒂托

*   [如何在 CI/CD 管道中为无服务器应用生成部署工件](https://seed.run/blog/how-to-generate-deployment-artifacts-in-your-cicd-pipeline-for-serverless-apps.html)，escrito origination e por[Jack Jiang](https://www.linkedin.com/in/jack-jiang-52b147a4/)