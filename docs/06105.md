# 如何使用 Helmfile 升级到 istio 1.2.4

> 原文：<https://dev.to/lykon/how-to-upgrade-to-istio-1-2-4-using-helmfile-3139>

我们的 istio 装置面临着一个有趣的挑战。我们运行 istio 版本 1.1.7 有一段时间了，想升级到最新的稳定版本。如果您遵循 [istio 网站](https://istio.io/docs/setup/kubernetes/upgrade/steps/)中的升级说明，这是一项简单的任务。

我们使用 [helmfile](https://github.com/roboll/helmfile) 来管理集群中的多个标准应用程序。在这个 helmfile 中是我们的 istio 装置。不幸的是，我无法找到一种简单的方式来运行`helmfile sync`并升级 istio 安装。

我想与您分享我们如何升级 istio 版本的一步一步。

## 头盔文件

在迁移之前，我们有一个 helmfile，声明我们的 istio 安装如下:

```
helmDefaults:
  kubeContext: my_cluster
  atomic: true

repositories:
- name: incubator
  url: https://kubernetes-charts-incubator.storage.googleapis.com/
- name: istio
  url: https://storage.googleapis.com/istio-release/releases/1.1.7/charts/

releases:
  - name: istio-init
    namespace: istio-system
    chart: istio/istio-init

  - name: istio
    namespace: istio-system
    chart: istio/istio
    version: ~1.1.7
    values: [./istio/values.yaml] 
```

> 在开始迁移之前，我建议阅读一下 [istio 升级说明](https://istio.io/docs/setup/kubernetes/upgrade/steps/)。

## 步骤 1 -检查您的自定义资源定义(CRD)

如果你像我们一样在文件中设置了`istio-init`图表，那么你可能会遇到一个著名的错误，由于现有的 CRD，你无法安装新的图表。

对我们来说，最简单的方法是去掉一些，让 istio 图表重新创建它们。

> 请注意，在继续升级之前，建议您备份当前的自定义资源数据。

```
$ kubectl get crds | grep ‘istio.io\|certmanager.k8s.io’ | cut -f1-1 -d “.” | \
    xargs -n1 -I{} sh -c “kubectl get —all-namespaces -oyaml {}; echo —“ > $HOME/ISTIO_1_0_RESTORE_CRD_DATA.yaml 
```

现在让我们改变我们的 helmfile 来拉新的 istio 版本:

```
repositories:
- name: incubator
  url: https://kubernetes-charts-incubator.storage.googleapis.com/
- name: istio
  url: https://storage.googleapis.com/istio-release/releases/1.2.4/charts/

releases:
  - name: istio-init
    namespace: istio-system
    chart: istio/istio-init

  - name: istio
    namespace: istio-system
    chart: istio/istio
    version: ~1.2.4
    values: [./istio/values.yaml] 
```

我们写了一个简单的脚本，试图清理一些东西，比如`ClusterRole`、`ClusterRoleBinding`、`Attributes`、`Metrics`定义和`PodDisruptionBudget`。这里要小心，请花点时间看一下剧本，能删的就只删。

```
#!/bin/sh

# Download the most recent version of the charts
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.2.4 sh -

cd istio-1.2.4

# install the new version’s CRDs
helm upgrade --install --force istio-init install/kubernetes/helm/istio-init --namespace istio-system

## Check if all jobs are done
kubectl get job --namespace istio-system | grep istio-init-crd

# Here is where the cleanup will start
# First we have to remove the current istio’s cluster roles
kubectl delete clusterrole istio-egressgateway-istio-system istio-ingressgateway-istio-system

# Now the cluster role bindings
kubectl delete clusterrolebinding istio-egressgateway-istio-system istio-ingressgateway-istio-system istio-kiali-admin-role-binding-istio-system

# Moving on, to remove the Kubernetes attributes
kubectl delete kubernetes attributes

# Next, we delete all metric CDRs that istio had installed
kubectl delete metric requestcount requestduration requestsize responsesize tcpbytereceived tcpbytesent tcpconnectionsopened tcpconnectionsclosed

# Finally we remove the PodDisruptionBudget
kubectl delete poddisruptionbudget istio-egressgateway istio-egressgateway istio-ingressgateway istio-pilot istio-policy istio-telemetry 
```

请注意，该脚本中的命令可能会返回如下内容:

`Error from server (NotFound): metrics.config.istio.io "requestcount" not found`

这意味着您没有安装 CRD，当您运行 istio 升级时，它会为您安装。

## 更新 istio

最后，运行 helmfile sync 命令来升级您的 istio 安装。

```
$ helmfile -f helmfile.yaml sync 
```

要确认一切正常，请运行:

```
$ istioctl version

client version: 1.2.4
citadel version: 1.2.4
egressgateway version: 94746ccd404a8e056483dd02e4e478097b950da6-dirty
galley version: 1.2.4
ingressgateway version: 94746ccd404a8e056483dd02e4e478097b950da6-dirty
pilot version: 1.2.4
policy version: 1.2.4
sidecar-injector version: 1.2.4
telemetry version: 1.2.4 
```

检查您的 Kiali 安装和新仪表板

```
kubectl -n istio-system port-forward (kubectl -n istio-system get pod -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001 
```

前往 [Kiali 控制台](http://localhost:20001/kiali/console)查看您的新版本。

## 结论

就是这样，简单的 istio 升级。我肯定有更好的方法，但这是最简单快捷的方法，对我们来说效果很好。

如果你知道更好的方法，请分享它，因为它会很有帮助。

谢谢👋