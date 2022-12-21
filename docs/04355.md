# Kustomize:使用新的 Kubernetes 模板系统部署应用程序

> 原文：<https://dev.to/colinjfw/kustomize-deploying-applications-using-the-new-kubernetes-templating-system-342o>

这是一系列实用、安全和可重复的 Kubernetes 部署方法的一部分。这些帖子是构建[递送机器人](http://deliverybot.github.io)的一部分，是从 GitHub 部署到 Kubernetes 之间缺失的一环。

我最近去调查了 Kubernetes 在部署自动化方面的一些情况，以了解社区中新开发的内容。我很快发现了一个新的模板系统 Kustomize。从我们的角度来看，它并不是一个严格意义上的模板系统——更好的描述方式是“为您的 Kubernetes yaml 文件定制”——但对我们大多数人来说，它实现了相同的目标。

首先，这个 Kustomize 是什么东西？实际上，Kustomize 是一个覆盖系统，允许您覆盖 Kubernetes 清单中的值。它为我们提供了一个目录结构，如下所示:

```
~/app
├── base
│   ├── deployment.yaml
│   └── kustomization.yaml
└── overlays
    ├── development
    │   ├── deployment.yaml
    │   ├── kustomization.yaml
    └── production
        ├── deployment.yaml
        └── kustomization.yaml 
```

覆盖图提供了在您的核心 Kubernetes 清单中更改值的机制。类似 Helm 的主要区别之一是覆盖是用常规的 Kubernetes yaml 语法指定的。这是 Kustomize 的关键创新。我可能有一个非常简单的“基本”Kubernetes 清单，如下所示(为了简洁起见，省略了一些 yaml):

```
# ~/app/base/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 1
  ... 
```

现在，在我的特定于环境的覆盖文件中，我可以包含一个覆盖，它会针对特定的环境碰撞副本。例如:

```
# ~/app/overlays/production/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 3
  ... 
```

现在，当我将清单应用到 Kubernetes 中时，我可以选择生产覆盖图。Kustomize 找出了差异，并将配置合并在一起。这导致我的部署资源的 3 个副本在生产中运行。在我的临时服务器上，我可以只指定一个副本，或者改变资源的标签或名称。

这是 Kustomize 的第一个强大功能，可能是最有用的。覆盖特性允许使用裸 Kubernetes yaml，但是可以在不同的环境之间改变值。

### 应用 Kustomize 配置

上面显示的树中的 Kustomization 文件相对简单。他们只是描述他们负责的清单。基本 Kustomize 文件如下所示:

```
# ~/app/base/kustomization.yaml
resources:
- ./deployment.yaml 
```

覆盖文件简单地指定了基础:

```
# ~/app/base/kustomization.yaml
resources:
- deployment.yaml
bases:
- ../../base 
```

实际上，应用这些文件很简单:

```
kubectl apply -k overlays/production 
```

Kustomize 是一个单独的 CLI，您可以使用它。在这里，使用最新版本的 kubectl，我们可以简单地将它作为一个标志来触发新的行为。在这个阶段，我们已经成功地将清单应用到了 Kubernetes 中，只使用了 Kubernetes 清单和 kubectl 中内置的工具，生产和登台之间略有不同。

### 更复杂的变换

让我们探索 Kustomize 的几个更强大的功能。Kustomize 背后的核心思想之一是“变压器”的概念这是 Kustomize 内置的代码，它根据一组声明性的简单规则转换您的 Kubernetes 清单。你可以定制这些转换器，但是一些内置的非常有用。下面是一些最常见的可用转换的示例:

```
# kustomization.yaml
namespace: production
namePrefix: production-
nameSuffix: -v1 
```

对于我们加载的每个资源，我们可以用提供的字符串作为资源名称的前缀和后缀。这对于在同一名称空间内部署同一组清单非常有用。我们还可以将名称空间应用于所有资源，只需指定名称空间参数。

可能每天最有用的转换之一是图像转换器。这会将 Kubernetes 清单中的图像标签更改为新版本:

```
# kustomization.yaml
images:
- name: nginx
  newTag: v1 
```

如果我们有一个指定 nginx 映像的部署，在 Kustomize 完成它的工作之后，这个新映像现在将应用标签“v1”。这是一个非常有用的转换，可以在部署之前应用到 CI 管道中。Kustomize CLI 具有编辑命令，可以动态地将这样的语句添加到 Kustomize 文件中。

## 为什么要用 Kustomize？

对于许多公司来说，与 Kubernetes 合作意味着深入掌舵。Helm charts 已经成为 Kubernetes 上航运应用程序的事实标准。对于许多用例来说，Helm 有一些不必要的复杂性。helm 的一些常见问题:

*   服务器端 tiller 组件在 Kubernetes 集群中拥有太多的权力。

*   helm 应用资源的方式有时会导致期望状态和实际状态之间的差异。

对于较小的 Kubernetes 部署来说，使用 helm 作为模板语言的部署系统是很常见的，部署步骤如下:

```
helm template ./charts/my-chart | kubectl apply -f - 
```

这和 Kustomize 的目标非常相似。如果您需要在 Kubernetes 清单中简单地替换变量和覆盖简单的值，那么 Kustomize 可能会在这方面提供更好的灵活性。

### 一些问题

我使用 Kustomize 遇到的第一个障碍是缺乏文字变量。如果你想做类似的事情:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: app
spec:
  rules:
  - host: "{host}" 
```

你和 Kustomize 的运气很差。推荐的方法是用 JSON 补丁管理这些，在配置文件中如下所示:

```
- op: replace
  path: /spec/rules/0/host
  value: foo.bar.io 
```

不幸的是，我认为对于很多团队来说，这种维护配置的方法会很快变得难以操作。如果您有一个未定义的主机变量，这实际上可能是一个非常严重的配置问题。Kustomize 团队的官方建议是在 Kustomize 配置中使用 envsubst 或其他模板语言。

## 总结

本质上，Kustomize 是对 Kubernetes 工具包的一个有用的补充。它可以解决您可能会遇到的一些更繁琐的模板问题，但它肯定不是完整的答案。对于希望发布应用程序的团队来说，Helm 可能是目前最简单、最久经考验的解决方案。

[我整理了一些使用 Kustomize](https://gist.github.com/colinjfw/58ec321708b761b79c0fd9c33eec8716) 进行部署的脚本，以后我可能会用它们来部署到 Kubernetes。请检查它们，并随时评论您的改进。

*敬请关注本系列的下一篇文章，探索实用、安全和可重复的 Kubernetes 部署方式的不同选项。*