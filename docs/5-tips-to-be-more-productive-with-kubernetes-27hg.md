# 提高 Kubernetes 工作效率的 5 个技巧

> 原文：<https://dev.to/peterj/5-tips-to-be-more-productive-with-kubernetes-27hg>

最初发布于[学云原生博客](https://www.learncloudnative.com/blog/2019-08-06-5_tips_to_be_more_proudctive_with_kubernetes/)。

我喜欢阅读和了解人们在使用 Kubernetes 和 Istio 时如何设置他们的环境以及他们使用的任何工具、提示和技巧，以提高工作效率。以下是我每天使用的 5 个技巧和工具，我认为它让我在使用 Kubernetes 和 Istio 时更有效率。

## 1。在 Kubernetes 上下文之间切换

如果您正在使用一个本地 Kubernetes 实例和一个或多个 Kubernetes 的云实例，您将在某些时候需要在上下文之间切换。Kubernetes CLI ( `kubectl`)提供了允许您处理不同上下文的命令:

*   当前上下文
*   获取上下文
*   重命名上下文
*   删除上下文
*   设置上下文-使用上下文

假设您知道想要切换到的 Kubernetes 上下文的名称，您可以使用下面的命令:

```
kubectl config use-context [CONTEXT-NAME] 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用多个集群，并且不知道上下文名称，那么您需要首先列出上下文，然后像这样运行 use-context 命令:

```
$ kubectl config get-contexts
CURRENT   NAME            CLUSTER         AUTHINFO        NAMESPACE
*         docker-desktop  docker-desktop  docker-desktop
          minikube        minikube        minikube
          cloudc          crdambvg43d     user-crdambvg43d

$ kubectl config use-context minikube 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，有一种更简单的方法可以做到这一点。我正在使用一个叫做 [kubectx](https://github.com/ahmetb/kubectx/) 的工具，它可以让你快速列出并切换到不同的 Kubernetes 上下文。要列出上下文，可以像这样运行`kubectx`:

```
$ kubectx
docker-desktop
minikub
cloudc 
```

Enter fullscreen mode Exit fullscreen mode

切换到另一个上下文就这么简单:

```
$ kubectx [CONTEXT-NAME] 
```

Enter fullscreen mode Exit fullscreen mode

## 2。在 Kubernetes 名称空间之间切换

使用 Kubernetes 时，另一件很常见的事情是使用来自多个名称空间的资源。例如，您可能希望在一个名称空间中列出 pod，在另一个名称空间中检查服务，等等。我在这里的工作流程是使用 Kubernetes CLI 支持的`--namespace`标志。例如，要获得名为`test`的名称空间中的所有窗格，可以运行`kubectl get pods -n test`。默认情况下，如果您不提供名称空间标志，将使用默认的 Kubernetes 名称空间——它被恰当地命名为 *default* 。

这个默认值可以在 kubeconfig 文件中更改——例如，您可以将默认名称空间设置为 *test* 或 *kube-system* 或任何其他名称空间。这样，在查询资源时就不需要使用`--namespace`标志。然而，改变这一点的命令是笨拙的:

```
$ kubectl config set contexts.my-context.namespace my-namespace 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令修改了`my-context`上下文中的名称空间字段，并将其更改为`my-namespace`。这意味着如果您切换到`my-context`并运行`kubectl get pods`，您将只能看到来自`my-namespace`名称空间的 pod。

与`kubectx`工具一起，你还会得到一个叫做`kubens `的工具——它帮助你列出和切换到不同的名称空间。
T3】

```
$ kubens
default
docker
kube-node-lease
kube-public
kube-system 
```

Enter fullscreen mode Exit fullscreen mode

为选定的上下文设置默认的名称空间也很简单快捷:

```
$ kubens default
Context "docker-desktop" modified.
Active namespace is "default". 
```

Enter fullscreen mode Exit fullscreen mode

## 3。别名 the kuble CLI

这是一个简单的提示。如果你和 Kubernetes 一起工作，你将会输入很多次`kubectl`,并且在某些时候你会厌倦输入全名。你可能认为它只有七个字符，但它加起来。

这里的提示是将`kubectl`别名为更短的东西，`k`例如:

```
$ alias k=kubectl
$ k get po
NAME    READY   STATUS    RESTARTS   AGE
mypod   1/1     Running   18         43h 
```

Enter fullscreen mode Exit fullscreen mode

理想情况下，您应该将别名`k=kubectl`放在您的`bash_profile`中，这样每当您打开终端时它就会被设置。

## 4。在 Kubernetes 集群中获得一个终端

当访问集群内部运行的服务和 pod 时，您要么需要公开它们，以便可以从公共互联网访问它们，要么在本地机器和集群内部运行的服务之间运行 kube 代理或转发端口。

但是，有时您希望运行普通的 curl 命令，而不暴露任何服务或转发端口。为此，我使用了一个作为 bash 概要文件的一部分加载的函数，该函数在集群中运行一个带有 *radial/busyboxplus:curl* 映像的 pod，并允许我访问终端。这样，我可以针对集群内部的服务和 IP 运行 curl。我调用函数`kbash`并像这样使用它:

```
$ kbash
If you don't see a command prompt, try pressing enter.
[ root@curl:/ ]$ 
```

Enter fullscreen mode Exit fullscreen mode

在提示符下，我可以针对内部 Kubernetes DNS 名称或 IP 地址运行 curl。要退出，只需键入`exit`，如果您想重新连接到 pod，运行`kbash`，它将连接到现有的 pod。我也将这个函数定义为我的[点文件](https://github.com/peterj/dotfiles)的一部分。

## 5。快速打开 Grafana/Jaeger/Kiali(或其他任何东西)

如果你计划使用 Istio 服务网格，你可能会在某些时候使用 Grafana/Jaeger/Kiali。访问这些服务需要您首先获取 pod 名称，然后设置一个转发到该 pod 的端口，最后打开浏览器找到转发的地址。每次输入的命令都很长:

```
$ kubectl get pods --namespace istio-system -l "app=grafana" -o jsonpath="{.items[0].metadata.name}"
grafana-6fb9f8c5c7-hrcqp
$ kubectl --namespace istio-system port-forward grafana-6fb9f8c5c7-hrcqp 3000:3000
$ open http://localhost:3000 
```

Enter fullscreen mode Exit fullscreen mode

更简单快捷的方法是为每个服务创建函数或别名。例如，我在一个文件中为 Grafana/Jaeger/Kiali 做了如下设置，该文件作为我的 bash 概要文件的一部分被加载:

```
#!/bin/bash
export GRAFANA_POD=$(kubectl get pods --namespace istio-system -l "app=grafana" -o jsonpath="{.items[0].metadata.name}")
export JAEGER_POD=$(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}')
export KIALI_POD=$(kubectl -n istio-system get pod -l app=kiali -o jsonpath='{.items[0].metadata.name}')
alias grafana="kubectl --namespace istio-system port-forward $GRAFANA_POD 3000:3000 & open http://localhost:3000"
alias jaeger="kubectl --namespace istio-system port-forward $JAEGER_POD 16686:16686 & open http://localhost:16686"
alias kiali="kubectl --namespace istio-system port-forward $KIALI_POD 20001:20001 & open http://localhost:20001" 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我想打开 Jaeger，我可以运行`jaeger`，它将获得 pod 名称，创建端口转发并打开浏览器。

如果您在经常打开的集群中运行其他服务，您可以用同样的方式设置别名。

你有什么最喜欢的让你更有效率的技巧/工具吗？