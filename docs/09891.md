# GitOps:使用 Flux 在 Azure Kubernetes 服务(AKS)中部署应用程序

> 原文：<https://dev.to/cmendibl3/gitops-deploying-apps-in-azure-kubernetes-service-aks-with-flux-4ali>

最近我了解了 GitOps，这是一种管理 Kubernetes 集群和使用 Git 在其上运行的应用程序的方法。其思想是，您可以在 Git 中声明性地描述系统的期望状态，并在合并发生时立即推出更改。

您可以立即看到这种方法的主要好处:您的 Git 存储库成为基础设施和应用程序代码的唯一来源，允许团队提高生产率和稳定性(您可以获得 Git 日志来审计变更)。

要实现 GitOps，您可以按照一些简单的步骤使用和配置 [Flux](https://docs.fluxcd.io/en/latest/) :

## 1。下载头盔模板

```
helm fetch `
  --repo https://fluxcd.github.io/flux `
  --untar `
  --untardir .\.charts `
  --version 0.10.2 `
  flux 
```

Enter fullscreen mode Exit fullscreen mode

## 2。烘烤模板与您的回购(我不使用蒂勒)

```
helm template flux `
  --set git.url="git@github.com:cmendible/kubernetes.samples" `
  --set git.path="19.flux" `
  --set git.pollInterval="5s" `
  --namespace flux `
  --output-dir .\.baked .\.charts\fluxcd 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我正在配置 [Flux](https://docs.fluxcd.io/en/latest/) 来使用我的 k8s 示例 repo 和 [19.flux](https://github.com/cmendible/kubernetes.samples/tree/master/19.flux) 文件夹，其中包含一个简单的部署[文件](https://github.com/cmendible/kubernetes.samples/blob/master/19.flux/dni-function.yaml)，但是当然你可以有更多的资源定义。

## 3。将配置部署到您的集群

```
kubectl apply -f .\.baked\flux\templates\ 
```

Enter fullscreen mode Exit fullscreen mode

## 4。获取 fluxctl CLI

下载 [fluxctl CLI](https://github.com/fluxcd/flux/releases/tag/1.13.2)

## 5。使用 fluxctl 获取公钥

```
fluxctl identity --k8s-fwd-ns flux 
```

Enter fullscreen mode Exit fullscreen mode

这个密钥是将您的集群状态与 Git 存储库(GitHub)同步所必需的:复制您获得的密钥，并使用它在您的 GitHub 存储库上创建一个具有写访问权限的**部署密钥**(设置>部署密钥>添加部署密钥>选中允许写访问权限>粘贴 Flux 公钥>单击添加密钥)

你都准备好了。如果一切顺利，您将在集群中发现一个名为 **dni-function** 的新部署。

要了解更多关于 GitOps check: [Weaveworks](https://www.weave.works/technologies/gitops/) 点击这里了解更多关于 [Flux](https://docs.fluxcd.io/en/latest/)

希望有帮助！