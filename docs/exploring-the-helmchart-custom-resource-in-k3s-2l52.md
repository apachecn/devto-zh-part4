# 探索 k3s 中的 HelmChart 自定义资源

> 原文：<https://dev.to/blakec/exploring-the-helmchart-custom-resource-in-k3s-2l52>

[k3s](https://k3s.io/) 是由 [Rancher](https://rancher.com/) 开发的轻量级 [Kubernetes](https://kubernetes.io/) 发行版，适用于 edge、 <abbr title="Internet of Things">IoT</abbr> 和资源受限的计算环境。

Helm 是 Kubernetes 的一个包管理器，它使用户能够找到、分享和使用为 Kubernetes 构建的软件。

传统上，Helm 由客户端(`helm`)和服务器端组件 [Tiller](https://helm.sh/docs/glossary/#tiller) 组成，后者管理 Helm charts 的部署和生命周期。在 k3s 中，舵柄被[舵控制器](https://github.com/rancher/helm-controller)取代。舵控制器定义了一个新的`HelmChart` [自定义资源定义](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)，或 <abbr title="Custom Resource Definition">CRD</abbr> ，用于管理舵图表。

您可以通过使用`kubectl`列出可用的 API 资源来验证`HelmChart`资源是否存在于 k3s 集群上。

```
 $  sudo k3s kubectl api-resources --api-group=helm.cattle.io
NAME         SHORTNAMES   APIGROUP         NAMESPACED   KIND
helmcharts                helm.cattle.io   true         HelmChart 
```

Enter fullscreen mode Exit fullscreen mode

## helm chart 资源

下面是一个`HelmChart`资源定义的例子。

```
---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata: # Kubernetes object metadata
  name: # Chart name (required)
  namespace: kube-system # Namespace Helm Controller is monitoring to deploy charts (required)
spec: # resource specification 
```

Enter fullscreen mode Exit fullscreen mode

`apiVersion`和`kind`字段一起将此标识为由 Helm Controller 管理的 HelmChart 资源。

`metadata`下的`name`字段指定了 HelmChart 资源的名称。`namespace`指定了 [Kubernetes 名称空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)，其中 Helm Controller 正在监控 CRDs。对于 k3s，这必须是`kube-system`,因为 Helm Controller 仅被配置为监视此名称空间的新 HelmChart 资源。

最后，`spec`或*规范*字段用于指定 HelmChart 资源的期望状态。

### HelmChart 资源`spec`

HelmChart 资源支持几个配置[参数](https://github.com/rancher/helm-controller/blob/3e223ca9dc94607ea9b7deb7f632551230c4db32/pkg/apis/helm.cattle.io/v1/types.go#L20-L25)。这些参数对应于`helm install`命令行参数，并在图表部署期间传递给 Helm。

*   `chart`(字符串:空)-图表引用或 URL。
*   `targetNamespace` (string: null) -安装图表的名称空间。如果省略，则默认为 k3s 控制器使用的名称空间(即 kube-system)。
*   `version`(字符串:null)要安装的精确图表版本。如果未指定，将安装最新版本。
*   `repo`(字符串:空)-定位所请求图表的图表资料档案库 URL。
*   `set`(字符串:空)-由配置值组成的字典/映射，提供给 Helm。键应该被指定为字符串。值可以是整数或字符串。如果需要更复杂的值，使用`valuesContent`。
*   `valuesContent`(字符串:空)-提供给 Helm 的配置值列表。这应该是多行字符串。

下面是一个 HelmChart 资源定义的示例，其中定义了所有可用的配置值。

```
---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: example-helmchart
  namespace: kube-system
spec:
  chart: test-helmchart
  targetNamespace: test-namespace
  version: 0.0.1
  repo: https://example.com/helm-charts
  set:
    key1: "value1"
    key2: "value2"
  valuesContent: |-
    config:
      application:
        admin_username: admin
        admin_password: insecure_password
      server:
        port: 8080
        tls_enabled: false 
```

Enter fullscreen mode Exit fullscreen mode

相应的 CLI 参数将是:

```
$  helm install test-helmchart --namespace test-namespace \
 --version 0.0.1 \
                              --repo https://example.com/helm-charts \
                              --set-string key1=value1 \
                              --set-string key2=value2 \
                              --values values.yaml 
```

Enter fullscreen mode Exit fullscreen mode

其中 values.yaml 包含出现在`valuesContent`字段下的内容。

*注:`set`和`valuesContent`都可以用来配置图表。`set`只支持字符串或整数作为值。如果需要其他数据类型，使用`valuesContent`。*

## 创建 HelmChart

我们现在将通过创建 HelmChart 资源定义来部署 [Node-RED](https://nodered.org/) 的过程，这是一种基于流程的物联网编程。

以下步骤是在运行 [Raspian](https://en.wikipedia.org/wiki/Raspbian) 操作系统的 Raspberry Pi 4 上执行的。如果使用不同的环境，您可能需要修改命令。

首先，使用默认编辑器创建一个名为`node-red-helmchart.yaml`的文件。

```
$  editor node-red-helmchart.yaml 
```

Enter fullscreen mode Exit fullscreen mode

将以下文本块复制并粘贴到文件中。

```
---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: node-red
  namespace: kube-system
spec:
  chart: stable/node-red
  targetNamespace: node-red
  set:
    image.tag: rpi
  valuesContent: |-
    ingress:
      enabled: "true"
      annotations:
        kubernetes.io/ingress.class: traefik
        traefik.ingress.kubernetes.io/rule-type: PathPrefixStrip
      hosts:
        - raspberrypi1.local
      path: /node-red/ 
```

Enter fullscreen mode Exit fullscreen mode

*注意:一定要修改`ingress.hosts`字段，以包含 Kubernetes 集群中一个或多个节点的主机名和/或 IP 地址。*

通过修改几个可用的[配置值](https://github.com/helm/charts/tree/master/stable/node-red#configuration)，对图表的部署进行了定制。特别是，已经启用了入口支持，将 Node-RED 暴露给位于 http://raspberrypi1.local/node-red/[的 Kubernetes 集群之外的主机。](http://raspberrypi1.local/node-red/)

## 安装 HelmChart

在 k3s 中安装 HelmChart 有两种方法。

1.  使用`kubectl apply`安装资源定义。
2.  在`/var/lib/rancher/k3s/server/manifests/`将资源定义复制到服务器的清单目录。

### 通过 kubectl 安装

使用`kubectl apply`指示 Kubernetes 安装指定的资源定义。

```
$  sudo k3s kubectl apply --filename node-red-helmchart.yaml
helmchart.helm.cattle.io/node-red created 
```

Enter fullscreen mode Exit fullscreen mode

这是在 Kubernetes 中供应资源的标准方法。

### 使用自动部署清单安装

k3s 包括一个名为[的自动部署清单](https://rancher.com/docs/k3s/latest/en/configuration/#auto-deploying-manifests)的功能，允许自动部署放置在 k3s 清单目录(`/var/lib/rancher/k3s/server/manifests`)中的舵图(和 Kubernetes 清单)。舵控制器持续监视该目录以寻找新的 CRD。当它检测到添加了新资源时，它会自动部署图表。因此，您可以通过简单地将`node-red-helmchart.yaml`复制到服务器的清单目录中来启动安装。

```
$  sudo cp node-red-helmchart.yaml /var/lib/rancher/k3s/server/manifests/node-red-helmchart.yaml 
```

Enter fullscreen mode Exit fullscreen mode

### 引擎盖下的安装流程

无论使用何种安装方法，当图表被部署时，Helm Controller 都会创建一个 Kubernetes [作业](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)，该作业使用`helm install`将图表安装到集群中。

在 kube-system 名称空间中执行`kubectl get jobs`,并搜索与已部署图表相关的作业。

```
$  sudo k3s kubectl --namespace kube-system get jobs \
 --selector helmcharts.helm.cattle.io/chart=node-red
NAME                    COMPLETIONS   DURATION   AGE
helm-install-node-red   0/1           21s        23s 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过重新运行`kubectl get jobs`来查看作业正在执行的命令，并使用 kubectl 的[定制列](https://kubernetes.io/docs/reference/kubectl/overview/#custom-columns)输出来附加附加参数以选择和显示特定于作业的信息。

```
$  export CUSTOM_COLUMN_OUTPUT="COMMAND:.spec.template.spec.containers[0].name,INSTALL_ARGS:.spec.template.spec.containers[0].args"
$  sudo k3s kubectl --namespace kube-system get job helm-install-node-red \
 --output=custom-columns=$CUSTOM_COLUMN_OUTPUT
COMMAND   INSTALL_ARGS
helm      [install --name node-red stable/node-red --namespace node-red --set-string image.tag=rpi] 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，该作业通过使用 HelmChart 资源中提供的安装参数列表执行`helm install`来安装图表。

### 验证图表安装

通过确保在 node-red 命名空间内创建了关联的资源，验证图表是否已成功部署。

```
$  sudo kubectl --namespace node-red get ingress,service,pods
NAME                          HOSTS                ADDRESS      PORTS   AGE
ingress.extensions/node-red   raspberrypi1.local   10.0.0.133   80      2m2s

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE service/node-red   ClusterIP   10.43.135.177   <none>  1880/TCP   2m2s
 NAME                            READY   STATUS    RESTARTS   AGE
pod/node-red-7c5c564cb6-sz8b8   1/1     Running   0          2m1s 
```

Enter fullscreen mode Exit fullscreen mode

## 更新 HelmChart

可以使用`kubectl apply`更新图表的配置。

```
$  sudo k3s kubectl apply --filename node-red-helmchart.yaml 
```

Enter fullscreen mode Exit fullscreen mode

## 删除 HelmChart

使用`kubectl delete helmchart`删除舵图。

```
$  sudo k3s kubectl --namespace kube-system delete helmchart node-red 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

您已经了解了 K3s 中可用的 HelmChart 自定义资源，如何构建 HelmChart 资源定义，将其安装在集群上，以及执行部署后操作(更新/删除)。

本演练到此结束。

我有一个编写的程序，`k3s-helmchart-generate`，它简化了构建 HelmChart 清单的过程。你可以在下面的 GitHub 上找到回购的链接。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [布雷克](https://github.com/blake)/[k3s-helm chart-生成](https://github.com/blake/k3s-helmchart-generate)

### 用于生成供 k3s 使用的 HelmChart CRDs 的帮助程序脚本