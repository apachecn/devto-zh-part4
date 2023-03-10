# 第 1 部分:如何从头开始构建数据平台

> 原文：<https://dev.to/spaghettifunk/part-1-where-it-all-begins-1d72>

有没有尝试过从零开始搭建数据平台？你知道从哪里开始，寻找什么吗？最重要的是:做什么*不做*？不再害怕！通过这些系列文章了解如何构建完美的数据平台:)本文将是系列文章的第一部分，它将让您了解我从零开始构建数据平台的过程。通过分享我的经验，开发它时学到的教训，某些设计选择的优缺点，以及我的目标，我希望让你的旅程不那么坎坷。

无论你是被赋予建立一个数据平台的任务，还是你决定在你自己的时间里做这件事:你有远大的抱负——或者至少我是这样做的。有野心是伟大的，为了让自己走得更远，有野心是必要的。这就是为什么当你开始设计某样东西时，重要的是你要比最初的*需求*看得更远。

闲话少说，让我们从讨论我为当前工作创建数据平台的动机开始这个旅程。

## 动机(又名需求)

我在媒体行业工作，我工作的公司没有像网飞、GitHub 或 Twitter 这样的技术部门。我们可能不被认为是一家“科技公司”，我们有一个伟大的工程师团队，他们像我一样有动力去创造美好的东西。

有一天，我们经理进来说，我们公司决定从零开始搭建一个数据平台。我们得到的要求如下:

*   它需要可扩展
*   它需要接收大量数据
*   它需要能够非常快速地查询

那就是“全部”！

工程团队讨论了我们想要使用什么类型的技术，以及架构应该是什么样子。我们决定更多地关注 Kubernetes。但是选择并不简单。团队的很大一部分人从未与 Kubernetes 合作过，他们对 AWS Lambda 和 API Gateway 有更多的了解。虽然对我来说，Kubernetes 是一个自然的选择，但对其他人来说，这有点可怕。

最后，我们一致认为使用 Kubernetes 会更好地满足我们的需求，特别是在可伸缩性和 Dockerized 应用程序的部署方面。使用它意味着我们必须将大量的服务从 ECS 和其他 EC2 实例迁移到 Kubernetes。

## 初始架构

在选择了 Kubernetes 之后，我们创建了一堆 bash 文件和 **Terraform** 脚本来开始。在工程团队中，有一个关于如何处理自动化的讨论，我们决定建立一个“一键部署”系统。

这个“一键式部署”系统由一个 Docker 映像组成，该映像包含我们生成数据平台的整个基础设施所需的所有包。在那之后，我们基于来自 Gruntwork 的 Yevgeniy Brikman 的这篇令人敬畏的[文章](https://blog.gruntwork.io/how-to-create-reusable-infrastructure-with-terraform-modules-25526d65f73d)创建了最初的骨骼。我们的结果看起来类似于这个[存储库](https://github.com/spaghettifunk/cluster-example)。

**提示:**当您的基础架构中有许多“移动”部件时，最好是将其自动化，并依靠工具来帮助您和您的团队以最小的努力将所有部件组合在一起。下图显示了最初的架构是什么样子的

[![Initial Architecture](img/34b2e3d9e93d45fb66a86061871ecc5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dp9TIVut--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/3cKjcxf/init.png)

这对我们来说很简单；我们需要[创建一个 Kubernetes 集群](https://github.com/terraform-aws-modules/terraform-aws-eks)，通过 NGINX 添加一个[负载平衡器。我们准备进入下一步。](https://github.com/nginxinc/kubernetes-ingress/blob/master/docs/installation.md)

## 好戏即将上演

我们只使用了一个集群，我们必须立即开始考虑如何部署应用程序。然后其他问题就蹦出来了；我们如何创建一个**试运行**环境，或者一个**测试**环境？最初的选择是用更少的机器扩展集群，并利用 **Kubernetes 名称空间**。

使用名称空间的好处是，你可以用一种类似于 T1 的方式`"isolate"`你的应用程序。隔离是我们试图实现的，这样我们就可以区分`environments`。这样做给命名和我们如何部署应用程序带来了问题。例如，让我们假设如下:我们有两个 API 和一个 WebApp。这三者都需要一个测试和登台环境，这样开发人员就可以在投入生产之前安全地部署他们的代码。因为我们决定使用名称空间，所以我们尝试首先创建三个名称空间:生产、登台、测试。将所有应用程序放在同一个名称空间下有一些缺点。第一个问题是删除一切会容易得多。例如，Kubernetes CLI ( `kubectl`)能够删除名称空间。如果开发人员发出下面的命令`kubectl delete ns <env>`,那么该名称空间下的所有应用程序都将消失。第二个问题在`isolation`。事实上，最初创建命名空间的原因是为了在`smaller virtual cluster`中对集群进行分区。

我们尝试了另一种方法:我们为每个环境的每个应用程序创建一个名称空间。例如，我们会有以下内容:

*   生产 _api1
*   staging_api1
*   测试 _api1
*   生产 _webapp
*   staging_webapp
*   测试 _webapp
*   等等...

可以清楚地看到，这会用大量的名称空间污染 Kubernetes 集群。下面的[文章](https://kubernetes.io/blog/2016/08/kubernetes-namespaces-use-cases-insights/)给了我们很多关于如何更好地使用名称空间的见解。尽管文章指出您可以使用我上面描述的两种方法，但它也强调了使用这种方法时的反模式。对我们来说，这些解决方案并不奏效。

我们做了什么？解决方案很简单:多个 Kubernetes 集群。

## 多个集群

如上所述，我们最初决定采用“1-click-deployment”策略:这意味着我们的代码库已经准备好部署我们想要的任意多的集群。我们做到了。我们完成的第二个架构如下图所示

[![Multiple clusters](img/888098a1ca7a3cbe27d7e462669693aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hyMLKEh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/9ymV46J/second.png)

如图所示，我们有三个集群。这样，我们可以根据我们想要的目标环境来部署应用程序。

整个团队都非常兴奋，我们认为我们做到了。但是，权力越大，责任越大。这里的职责是指**测井**和**监控**。对于三个集群，理解其运行情况相当具有挑战性。我们又一次遇到了需要解决的问题。

在具有多个应用程序的分布式环境中，理解正在发生的事情是至关重要的。事实上，能够在开发过程中快速调试您的应用程序，或者理解是什么导致了生产中的最终错误，是保持对系统控制的基本步骤。

但是仅仅记录是不够的。机器本身通常不写日志，这暴露了它们硬件的度量标准。因此，收集这些信息并将其可视化，这将允许您和您的团队根据某些规则设置**警报**。规则可以是“如果平均 CPU 使用率高于 80%，则触发警报”。警报本身可以是发送给团队的电子邮件或松弛消息。警报将防止您的机器达到不良状态。

## 控制飞机进行救援

当您有许多应用程序、不同的环境，以及一个渴望创建、测试应用程序或模型并将其投入生产的工程师和数据科学家团队时，您需要一个可靠的监控和日志记录系统。但是我们如何做到这一点呢？答案还是很简单:另一个 Kubernetes 集群:)

Controlplane 是我们从公司的另一个团队那里取的一个有趣的名字。这个集群的唯一目的是从其他集群收集指标和日志，并集中这些信息的可视化。此时，体系结构如下所示

[![Controlplane system](img/7e3a7fab2c0adc12a5c7e51deed90971.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bYlpUXVh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/mzD23s9/third.png)

在每个集群上，我们设置了 [**【普罗米修斯】**](https://prometheus.io/)[**【节点导出器】**](https://github.com/prometheus/node_exporter)[**Kube-state-metrics**](https://github.com/kubernetes/kube-state-metrics)来展示集群的所有度量。为了收集信息并将它们发送出集群，我们使用了**。所有的指标和日志都被重定向到一个 **Kafka 主题**。这样，我们就能够从控制面板上获取它们。**

 **在 controlplane 中，我们安装了 [**Logstash**](https://www.elastic.co/products/logstash) ，并将其连接到上面提到的 **Kafka Topic** ，这样它就可以开始获取日志。Logstash 是一个开源的服务器端数据处理管道，它同时从多个来源获取数据，对其进行转换，然后将其发送到输出。

现在 Logstash 已经可以访问日志了，我们只需要将它们重定向到 [**Elasticsearch**](https://github.com/elastic/elasticsearch) 服务，并用 [**Kibana**](https://github.com/elastic/kibana) 可视化它们。对于这些指标，我们制定了 [**Grafana**](https://grafana.com/) 并创建了一系列仪表板来可视化每个系统的状态。

有了控制面板，我们就可以完全可视化和控制每个集群的每个方面。控制面板是一个双赢的解决方案。

## 结论

在第一部分中，我解释了我们如何开始设计数据平台。本文主要关注底层基础设施和我们必须克服的问题。

在开发的这一阶段，我学到的经验是:比我设计软件时考虑得更多。软件比基础设施变化更快。设计基础设施时，需要考虑更多的变量，您做出的选择会对最终结果产生重大影响。长话短说:先思考，慢慢来。急于迅速解决问题并不总是能帮到你。

在下一篇文章中，我将讨论应用程序是如何部署的，以及团队如何与这个架构进行交互。**