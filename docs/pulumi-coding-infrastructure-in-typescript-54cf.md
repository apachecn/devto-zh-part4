# pulumi-Typescript 中的编码基础结构

> 原文：<https://dev.to/ksivamuthu/pulumi-coding-infrastructure-in-typescript-54cf>

*   [概述](#Overview)
*   [入门](#Getting-Started)
    *   [安装 Pulumi CLI](#Install-Pulumi-CLI)
    *   [先决条件](#Prerequisites)
*   [创建新的 EKS 集群](#Create-a-new-EKS-cluster)
*   [设置 NGINX 入口控制器](#Setting-up-NGINX-Ingress-Controller)
*   [在集群中部署红色节点](#Deploy-Node-RED-in-Cluster)
*   [想法&结论](#Thoughts--Conclusion)

## 概述

我将分享我对使用 Typescript 编码基础设施的最初想法和印象。我最近一直在使用 Terraform 编写 AWS EKS 基础设施的代码。我真的很喜欢 Terraform，它对基础设施的构建、更改和版本控制以及可移植性有很大帮助。毫无疑问，这是一个很棒的工具，但我正在探索其他选项，包括 [AWS CDK](https://github.com/awslabs/aws-cdk) ，它们更便于用我最喜欢的语言(Javascript/Typescript)编码，而不是 YAML 或 DSL(领域特定语言)和多云基础设施编码。

我遇到了[Pulumi](https://pulumi.io)——一个使用 Typescript 编写基础设施的工具。Pulumi 的 SDK 是完全开源和可扩展的，使您能够参与丰富的库生态系统，简化常见任务，从容器到无服务器到基础设施，以及介于两者之间的一切。

在本帖中，我们将介绍如何开始使用 Pulumi，创建 AWS EKS 基础设施，并在 Kubernetes 集群中部署基于节点红流的物联网(IoT)编程。

## 入门

### 安装 Pulumi CLI

首先，您必须安装 [Pulumi](https://pulumi.io) CLI 工具。如果你用的是 macOS，可以用 homebrew 安装。

```
$ brew install pulumi 
```

Enter fullscreen mode Exit fullscreen mode

如果你想在 Linux 中安装它，运行下面的脚本来安装。

```
$ curl -fsSL https://get.pulumi.com | sh 
```

Enter fullscreen mode Exit fullscreen mode

或者在 Cmd 中运行下面的脚本，将其安装到 Windows 中。

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; iex ((New-Object System.Net.WebClient).DownloadString('https://get.pulumi.com/install.ps1'))" && SET "PATH=%PATH%;%USERPROFILE%\.pulumi\bin" 
```

Enter fullscreen mode Exit fullscreen mode

安装后，通过运行 pulumi CLI:
打印 pulumi 版本，以验证一切正常

```
$ pulumi version 
```

Enter fullscreen mode Exit fullscreen mode

### 先决条件

安装其他先决条件以创建 AWS EKS，并使用 Typescript 开发基础结构。

*   安装 [NodeJS](https://nodejs.org/en/download/) (10.x 或更高版本)
*   安装 [npm]((https://nodejs.org/en/download/)) - NodeJS 包管理器
*   安装和配置 [AWS CLI](https://aws.amazon.com/cli/)
*   为 Kubernetes 安装 [AWS IAM 认证器](https://github.com/kubernetes-sigs/aws-iam-authenticator)

* * *

这篇博文中使用的示例代码在下面的报告中:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[ksivamuthu](https://github.com/ksivamuthu)/[eks-pulumi-demo](https://github.com/ksivamuthu/eks-pulumi-demo)

### 使用 Pulumi - Typescript 的 AWS EKS 和无节点部署

<article class="markdown-body entry-content container-lg" itemprop="text">

# 使用 Pulumi 的 AWS EKS 基础设施和节点 RED 部署

[![Deploy](img/1c5bd23c43b2e247854d9bd6a9aceb36.png)](https://app.pulumi.com/new)

## 目录

*   [AWS EKS 基础设施&节点红色部署使用 Pulumi](https://github.com/ksivamuthu/eks-pulumi-demo#AWS-EKS-Infrastructure--Node-RED-deployment-using-Pulumi)
    *   [目录](https://github.com/ksivamuthu/eks-pulumi-demo#Table-of-Contents)
    *   [概述](https://github.com/ksivamuthu/eks-pulumi-demo#Overview)
    *   [入门](https://github.com/ksivamuthu/eks-pulumi-demo#Getting-Started)
        *   [安装 Pulumi CLI](https://github.com/ksivamuthu/eks-pulumi-demo#Install-Pulumi-CLI)
        *   [先决条件](https://github.com/ksivamuthu/eks-pulumi-demo#Prerequisites)
    *   [创建新的 EKS 集群](https://github.com/ksivamuthu/eks-pulumi-demo#Create-a-new-EKS-cluster)
    *   [设置 NGINX 入口控制器](https://github.com/ksivamuthu/eks-pulumi-demo#Setting-up-NGINX-Ingress-Controller)
    *   [在集群中部署红色节点](https://github.com/ksivamuthu/eks-pulumi-demo#Deploy-Node-RED-in-Cluster)
    *   [想法&结论](https://github.com/ksivamuthu/eks-pulumi-demo#Thoughts--Conclusion)

## 概观

我将分享我对使用 Typescript 编码基础设施的最初想法和印象。我最近一直在使用 Terraform 编写 AWS EKS 基础设施的代码。我真的很喜欢 Terraform，它对基础设施的构建、更改和版本控制以及可移植性有很大帮助。毫无疑问，这是一个很棒的工具，但我正在探索其他选项，包括 [AWS CDK](https://github.com/awslabs/aws-cdk) ，它们更便于用我最喜欢的语言(Javascript/Typescript)编码，而不是 YAML 或 DSL(领域特定语言)和多云基础设施编码。

我遇到了[Pulumi](https://pulumi.io)——一个使用 Typescript 编写基础设施的工具。Pulumi 的 SDK 是完全开源和可扩展的…

</article>

[View on GitHub](https://github.com/ksivamuthu/eks-pulumi-demo)

## 创建新的 EKS 集群

*   用`pulumi new`创建一个空项目，并选择一个模板`aws-typescript`来创建一个最小的 AWS Typescript Pulumi 程序。

```
$ pulumi new 
```

Enter fullscreen mode Exit fullscreen mode

*   安装所需的 package.json 依赖项。

```
$ npm install --save @pulumi/eks @pulumi/kubernetes 
```

Enter fullscreen mode Exit fullscreen mode

*   在`index.ts`中添加以下代码

```
import * as pulumi from "@pulumi/pulumi";
import * as eks from "@pulumi/eks";
import * as awsx from "@pulumi/awsx";
import * as k8s from "@pulumi/kubernetes";

const name = "smart-iot";

const vpc = new awsx.ec2.Vpc("vpc", { subnets: [{ type: "public" }] });
const cluster = new eks.Cluster(name, {
    vpcId: vpc.id,
    subnetIds: vpc.publicSubnetIds,
    desiredCapacity: 2,
    minSize: 1,
    maxSize: 2,
    storageClasses: "gp2",
    deployDashboard: false,
});

// Export the clusters' kubeconfig.
export const kubeconfig = cluster.kubeconfig 
```

Enter fullscreen mode Exit fullscreen mode

*   要预览和部署更改，运行`pulumi up`并选择“是”up 子命令显示将要创建的资源的预览，并提示是否继续部署。

*   要使用 kubectl 访问您的 EKS 集群，我们需要设置 kubeconfig 文件并下载 kubectl

```
$ pulumi stack output kubeconfig > kubeconfig
$ export KUBECONFIG=`pwd`/kubeconfig

$ kubectl version
$ kubectl cluster-info
$ kubectl get nodes 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的 EKS 集群已经准备就绪。现在让我们部署 nginx-ingress 控制器来设置和进入。

## 设置 NGINX 入口控制器

安装`nginx-ingress` helm chart，使用 helm 包管理器在 Kubernetes 集群上引导 nginx-ingress 部署。只需使用指向新提供的 EKS 集群的 Pulumi Kubernetes 提供程序添加 helm chart 资源选项。

```
const resourceOptions: ComponentResourceOptions = {
    providers: { kubernetes: cluster.provider }
};

const nginxIngressChartOpts = {
    repo: 'stable',
    chart: 'nginx-ingress'
};

const nginxIngress = new k8s.helm.v2.Chart('nginx-ingress', nginxIngressChartOpts, resourceOptions); 
```

Enter fullscreen mode Exit fullscreen mode

获取入口 URL 并将其导出到 pulumi 输出。

```
export const appUrl = nginxIngress.getResourceProperty('v1/Service', 'nginx-ingress-controller', 'status')
    .apply(status => status.loadBalancer.ingress[0].hostname); 
```

Enter fullscreen mode Exit fullscreen mode

## 在集群中部署红色节点

Node-RED 是一个编程工具，用于以令人兴奋的新方式将硬件设备、API 和在线服务连接在一起。它提供了一个基于浏览器的编辑器，可以使用调色板中的所有节点轻松地将流连接在一起，只需单击一下就可以将这些节点部署到它的运行时。

红色节点舵图(不由红色节点维护)可在舵图稳定库获得。

```
const chartOpts = {
    repo: 'stable',
    chart: 'node-red',
    values: {
        config: {
            timezone: 'America/New_York'
        },
        ingress: {
            enabled: true,
            hosts: ['*.elb.amazonaws.com']
        }
    }
};

const nodeRed = new k8s.helm.v2.Chart('node-red', chartOpts, resourceOptions); 
```

Enter fullscreen mode Exit fullscreen mode

要预览和部署更新的更改，运行`pulumi up`并选择“yes”up 子命令显示了所创建资源的预览。

通过运行 pulumi stack output 命令获得 node-red 的应用程序 URL。

```
$ pulumi stack output appUrl 
```

Enter fullscreen mode Exit fullscreen mode

您可以导航到应用程序 URL 来查看 kubernetes 集群中运行的 Node-RED 应用程序。

[![](img/5bb7d7d0b37f2870653fcda67ba2ed77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dWSgseL1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/awtcszofpw014qzgf6jq.png)

## 思想&结论

这是在 EKS 集群中创建 EKS 集群、设置入口控制器和节点红色应用程序的全部代码。EKS 集群的第一次提升奇迹般地在`@pulumi/eks`和`@pulumi/awsx` Pulumi 人行横道上完成。与 terraform 一样，Pulumi 也支持本地和远程后端来存储您的[状态](https://pulumi.io/reference/state/)。最后，你要用你最喜欢的语言编写基础设施，这太棒了。

你应该试试 Pulumi，用 Typescript/Javascript 构建你的基础设施。