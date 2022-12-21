# 使用 AWS Codedeploy 将 Ruby 应用程序部署到 EC2:如何使用 Bundler！

> 原文：<https://dev.to/mknycha/deploying-ruby-app-using-aws-codedeploy-to-ec2-how-to-do-it-with-bundler-49gg>

夜深了，经过一周的努力，我成功地正确配置了我的部署。这篇短文的目的是向您展示我在使用 Codedeploy 部署 Ruby 应用程序时遇到的一些依赖问题，以及解决这些问题的方法。

先决条件:AWS Codedeploy 配置的基本知识(如果你通读了文档，你会做得很好)。

## 从头开始...

我有我的 Ruby 应用程序。它使用 Bundler 来安装 gem 依赖项。我把它放在 AWS EC2 上，效果非常好。
注意:在我的 EC2 上，我使用的是亚马逊 Linux AMI 2018.03.0 (HVM)，SSD 卷类型，因为它附带了 Ruby 和一些开箱即用的 CLI 工具。只差一件事让我高兴——当我的 bitbucket 存储库上的某个分支更新时，我会部署我的应用程序。
让我们假设我有一个正在运行的虚拟机，上面安装了 codedeploy-agent。
我推荐[这篇教程](https://hackernoon.com/deploy-to-ec2-with-aws-codedeploy-from-bitbucket-pipelines-4f403e96d50c)为你的 bitbucket 存储库设置部署管道。教程中还没有提到一件事:用 Bundler 安装应用程序依赖项。

## 问题 1

这有什么大不了的？
我可以使用`gem install bundler`,然后在 AppSpec 部署挂钩中运行的脚本中添加`bundle install`,对吗？