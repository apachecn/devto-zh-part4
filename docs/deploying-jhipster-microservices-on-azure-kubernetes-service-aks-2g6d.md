# 在 Azure Kubernetes 服务(AKS)上部署 JHipster 微服务

> 原文：<https://dev.to/deepu105/deploying-jhipster-microservices-on-azure-kubernetes-service-aks-2g6d>

*最初发布于[deepu . tech](https://deepu.tech/deploying-jhipster-microservices-on-azure-kubernetes-service-aks/)T3。*

如果您正在开发和部署生产应用程序，尤其是云，您应该听说过**。Kubernetes( **k8s** )是一个最初由 Google 开发的容器编排平台，它使得将容器化/码头化的应用程序部署到生产环境中变得更加易于管理和扩展。**

 **[**Kubernetes**](https://kubernetes.io/) 已经成为容器编排领域不可否认的冠军，我们看到涌现的其他 **K*S** 产品都证明了这一点。 **K** 显然代表 Kubernetes，而 **S/E** 代表 Service/Engine，第一个字母代表提供它的产品。到目前为止，我们已经有了[**AKS**](https://docs.microsoft.com/en-us/azure/aks/)(Azure)[**GKE**](https://cloud.google.com/kubernetes-engine/)(Google)，以及[**EKS**](https://aws.amazon.com/eks/)(Amazon ECS)和 [**PKS**](https://content.pivotal.io/pivotal-container-service-pks) (Pivotal)以及来自 [Oracle](https://cloud.oracle.com/en_US/containers/kubernetes-engine#) 和 RedHat(阅读 [Openshift](https://www.redhat.com/en/technologies/cloud-computing/openshift) )的一些口味

我的一个同事写了一篇关于它的很好的文章，我强烈推荐你也读一读。

在本文中，我们将了解如何将 JHipster 创建的微服务架构部署到 Azure Kubernetes 服务中。

Azure Kubernetes Service(AKS)是微软提供的托管 Kubernetes 平台，用于托管您的容器化应用程序。

### 创建微服务应用

在我之前的一篇[帖子](https://deepu.tech/create-full-microservice-stack-using-j-hipster-domain-language-under-30-minutes)中，我展示了如何使用**杰普斯特**和 **JDL** 创建全栈微服务架构，如果你想了解更多细节，请在这里阅读帖子[。在本练习中，我们将使用相同的应用程序。](https://deepu.tech/create-full-microservice-stack-using-j-hipster-domain-language-under-30-minutes)

让我们回顾一下所需的步骤。

创建一个 JDL 文件，比如 app.jdl，将下面的内容复制到其中。**