# 在 AWS 上部署 Phoenix 项目

> 原文：<https://dev.to/christopherlai/deploying-a-phoenix-project-on-aws-5d4>

这是我们目前如何在 AWS 上部署 Phoenix 项目的一个快速纲要。它不会包含任何代码或截图——只是一个高层次的概述。

虽然我们使用 Distillery 来构建我们的版本，但我不会在这里讨论这些细节。我建议查看一下关于酿酒厂的 Hex 文档。

## 概述

这是合并到`master`到`EC2`的基本流程。

`GitHub`>`AWS CodePipeline`>`AWS CodeBuild`>`AWS CodeDeploy`>T4】

## GitHub + AWS 代码管道

当一个拉请求被合并到`master`中时，AWS 代码管道被触发。这是通过 CodePipeline 使用 GitHub Webhooks 设置完成的。设置非常简单，AWS 有一些很好的入门文档。

然后，CodePipeline 将代码交给 CodeBuild 来构建我们的版本。

## 代码构建

我们使用 CodeBuild 上的自定义 Docker 映像来构建我们的版本。可以使用 CodeBuild 提供的默认 Docker 图像，但是我们发现构建自定义图像更适合我们的需要。我们的自定义映像非常简单，由以下内容组成:

*   Ubuntu 18.04
*   NodeJS 10LTS
*   占线小时
*   长生不老药

然后通过 CodeBuild 的工件配置选项将发布传递回 CodePipeline。

然后，CodePipeline 使用 CodeDeploy 触发部署。

**注意:当一个构建被 CodePipeline 触发时，您的 Git 历史是不可用的。**

## 代码部署

CodeDeploy 用于将上一步中由 CodeBuild 生成的发布直接推送到我们的 EC2 实例。这是由运行在每个 EC2 实例上的 CodeDeploy 代理完成的。代理会轮询可用的部署，如果有可用的部署，会自动进行部署。

我们所有的实例都运行一个定制的 AMI，它预先安装了 CodeDeploy 代理。我们使用 Packer 和 Ansible 构建我们的自定义 ami。我们的 EC2 实例由一个使用定制启动配置的自动扩展组管理。

整个过程简单快捷。从合并到完全部署大约需要 3-5 分钟。

## 奖金:生产凭证

我们的目标是把我们的 EC2 实例当作牛，而不是宠物。这意味着我们需要一种自动化和安全的方式将生产凭证和秘密传递给我们的 EC2 实例。我们不想在每个 EC2 实例上存储文本文件或环境变量。我们决定改用 AWS Secrets Manager。

我编写了一个定制的 Distillery 配置提供程序，当 Phoenix 发布开始时，它从 Secrets Manager 获取凭证。Secrets Manager 将 TOML 返回给自定义配置提供程序，然后解析 TOML，并使用 Elixir `Application`模块设置配置。

我很乐意写更多的细节或回答任何问题。