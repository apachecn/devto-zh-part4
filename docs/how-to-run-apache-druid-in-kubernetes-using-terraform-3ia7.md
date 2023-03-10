# 如何使用 Terraform 在 Kubernetes 中运行 Apache Druid

> 原文：<https://dev.to/spaghettifunk/how-to-run-apache-druid-in-kubernetes-using-terraform-3ia7>

在本文中，我将展示如何创建一个 Terraform 模块，在这里我可以在 Kubernetes 上部署 Apache Druid。这不是生产就绪，但实时摄取测试已经相当成功。希望我的经历能对你有所帮助！

**首先:什么是阿帕奇德鲁伊？**

[![](img/80e93dd8c2b4cd68480950418d7f7307.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--thj6acws--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3128/1%2Ah9cBMCb1QtVU5VsYx_hRBg.png)

Apache Druid 是一个高性能实时分析数据库。Druid 最初创建于 [Metamarkets](https://metamarkets.com) ，后来捐赠给了 Apache 基金会，它被设计成容错、速度极快、可水平扩展和许多其他功能。这是一个庞大而复杂的项目，需要一些时间才能完全掌握它。

**准备缩放！**

我在与 Druid 一起工作时面临的最大挑战之一是以一种我可以轻松扩展的方式部署系统。我首先在 AWS 的 VPC 中创建一个机器群，然后在每个实例上启动更适合该类型节点的服务。例如，对于[代理](https://druid.apache.org/docs/latest/tutorials/cluster.html#query-server)，我选择了一个*通用*类型，而对于[历史](https://druid.apache.org/docs/latest/tutorials/cluster.html#data-server)，我使用了一个*存储优化的*机器。

[![Druid services](img/a529c1d1032a93d227910503259ce766.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6OTvTC5n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/1%2Aw1THsy_Ablf9rzUU9NotAA.png)

一旦所有的机器和服务都启动并运行，我必须确保所有的组件都能够相互连接并交换信息。我通过检查每个服务的日志来做到这一点。如果他们不连接，可能会导致一些网络错误或不适当的配置。为了让一切都完美和自动化，我选择了[地形](https://www.terraform.io/)结合[打包](https://www.packer.io/)和[流浪汉](https://www.vagrantup.com/)。经过几天的试验(和错误！)，我设法让德鲁伊集群启动并运行。然而，自动缩放仍然是一个问题。

## 库伯内斯在营救

我喜欢 Kubernetes 的一点是，当负载超过某个阈值时，它能够水平扩展 pod。我使用的集群能够在没有更多可用资源的情况下自动伸缩机器，因此它使 Kubernetes 成为部署 Druid 的良好候选。

**集群配置**

我再次使用 Terraform 来部署基础设施。因为 Terraform 有一个可用的 Kubernetes 提供者，所以我决定也通过 Terraform 设置一些服务，Druid 就是其中之一。所有其他应用程序，如 API、Web 应用程序等。最初手动部署，然后通过 CI/CD 进行更新。

我用不同的**节点组**部署了一个[亚马逊 EKS](https://aws.amazon.com/eks/) 集群，这样我就可以只在特定的节点上部署 Druid。如上所述，根据服务的不同，我需要不同类型的机器。 *autoscaler* 应用程序能够根据集群上的负载添加或删除机器。如何创建这样一个集群的例子可以在[这里](https://github.com/spaghettifunk/cluster-example)找到。

我用 8 台机器启动集群，分成 2 个不同的节点组。通过这种方式，我可以在不同的节点部署德鲁伊、动物园管理员和 Postgres，并让它“*容错*”(有一个[问题](https://github.com/spaghettifunk/druid-terraform/issues/8)对 pod-affinity 开放)。

## 是时候付诸行动了！

我创建的模块，可以通过让 Terraform 通过 **git** 下载来导入。该模块假设 Terraform 能够直接部署在 Kubernetes 上。这里有一个例子

导入后，您可以运行一个表单计划和(希望如此！)terraform 应用于您的集群。

## 结论

事实上，德鲁伊现在已经在 Kubernetes 建立了，这将允许你在没有人工干预的情况下自动升级系统。我创建的模块，需要一些帮助来使它变得更好和**生产就绪，**但是我认为如果你打算使用德鲁伊，这是一个好的开始。

> 非常欢迎对[项目](https://github.com/spaghettifunk/druid-terraform)的贡献🎉随意打开一个**公关**或一个**问题**。

有几个注意事项你需要记住。例如，如果您设置了[深度存储](https://druid.apache.org/docs/latest/dependencies/deep-storage.html)而没有使用 S3 或 HDFS，您可能会在保存数据方面遇到问题(我不建议为此使用 PVC)。

总体来说，扩大历史节点不是问题，但是缩小规模可能会导致信息丢失或者迫使德鲁伊重新索引数据。请确保您对此服务有良好的缩放规则。

如果你有任何关于这个话题的问题，我很乐意帮忙！你可以在 [Twitter](https://twitter.com/davideberdin) 和 [GitHub](https://github.com/spaghettifunk) 找到我。