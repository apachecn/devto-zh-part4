# 写一个非常基本的 kubernetes 变异录取 webook

> 原文：<https://dev.to/ineedale/writing-a-very-basic-kubernetes-mutating-admission-webhook-5b1>

我的发现，当试图写一个(非常)简单的 kubernetes 变异入学网页挂钩。

[![](img/430cdbe7b705b1f5997219026d34a7c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xGRDRDhB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABkllrXSkhpMTulJIk1b0aw.jpeg)

#### 好的..那么，webhooks…？

入学网页挂钩帮助你做一些很酷的事情，有两种网页挂钩，*验证*和*变异*。在本帖中，我们将集中讨论变异的录取网钩。

变异准入 webhooks 允许您"*修改"*(例如)Pod(或任何 kubernetes 资源)请求。例如，您可以修改一个 Pod 以使用一个特定的调度程序，添加/注入 sidecar 容器(想想 LinkerD sidecar)，或者如果它不符合某些安全要求，甚至拒绝它，等等。等等。—所有的**都不需要**编写一个完全成熟的“微”服务来做这件事。webhook 可以住在*的任何地方，*在实践中，k8s 只需要知道*在哪里*在哪里。

### 设置

设置很简单，但也很重要，您真正需要做的就是确保在 k8s api-server 中启用了 MutatingAdminssionController。要检查您的 k8s 集群是否启用了此功能，您可以使用

```
kubectl api-versions | grep admissionregistration 
```

对于开发，我可以推荐使用 Kubernetes-In-Docker (KinD)，你需要的只是 Docker 和 [KinD](https://kind.sigs.k8s.io/docs/user/quick-start/) 。KinD 不自动启用这些，可以使用这种 KinD 配置(kind.yaml)

```
---
kind: Cluster
apiVersion: kind.sigs.k8s.io/v1alpha3
kubeadmConfigPatches:
- |
 apiVersion: kubeadm.k8s.io/v1beta2
 kind: ClusterConfiguration
 metadata:
 name: config
 apiServer:
 extraArgs:
 "enable-admission-plugins": "NamespaceLifecycle,LimitRanger,ServiceAccount,TaintNodesByCondition,Priority,DefaultTolerationSeconds,DefaultStorageClass,PersistentVolumeClaimResize,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota"
nodes:
- role: control-plane 
```

用
旋转同类集群

```
kind create cluster --conifg kind.yaml 
```

我相信你将能够配置 kubectl 等。从现在开始使用这个集群。

就这样，设置完成了。干得好！:)

### 部署

我想从这里开始，因为这是这个*“教程”*(如果你愿意的话)中最容易的部分。部署准入 webhook 实际上与在 k8s 集群上部署任何其他服务是一样的。

我们只需要

*   广告服务
*   部署
*   变异的 webhook 配置

前两个很简单，所以我就不深究了，你可以看看我在 [Github](https://github.com/alex-leonhardt/k8s-mutate-webhook/blob/master/deploy/webhook.yaml) 上用了什么。

在您*之前，只需将它部署到 prod* ...

在生产环境中使用 webhooks 之前，请考虑几个假设:

*   如果对你的 webhook 的请求失败了(连接掉线，连接重置，没有 dns 等等)怎么办？)
*   如果部署失败，逃生舱卡在紧急回路中怎么办
*   如果 webhook 已经成为关键任务，并且 ***必须在 100%的时间里正常运行*** 该怎么办
*   如果你创建了一个循环依赖怎么办(我的最爱！)
*   如果您有一个 3214 节点的集群，其中有成千上万的资源和请求都依赖于这个 webhook，它可以扩展到 1 pod)

> 注意:webhook 只在 SSL/TLS 上被调用，所以你的 web hook(T2)必须有一个有效的签名证书

**MutatingWebhookConfiguration**是我们告诉 k8s 哪些资源请求应该发送给我们的 webhook 的地方。该配置由以下属性组成:

*   `apiVersion (at the time it is: admissionregistration.k8s.io/v1beta1)`
*   `kind (must be: MutatingWebhookConfiguration)`
*   `metadata (the usual: name, annotations, labels)`
*   `webhooks (a list of type webhook)`

webhook ( *类型*)由这些属性组成:

*   `name`
*   `clientConfig`
*   `- caBundle (we will get this from the k8s cluster itself)`
*   `- service to send the AdmissionReview requests to`
*   `rules ( a list of rules that define which resource operations should be matched, these rules make sure that k8s resource requests are sent to your webhook )`
*   `namespaceSelector (the usual: matchLabels: {“label_name”: “label_value”}`
*   `…`

还有更多的方法可以使用，对于一个简单的非生产 webhook 来说，上面的方法就足够了。

完整的酒店列表可以在[这里](https://godoc.org/k8s.io/api/admissionregistration/v1beta1#MutatingWebhook)看到。

规则由以下内容组成:

*   `operations (a list of [operations](https://godoc.org/k8s.io/api/admissionregistration/v1beta1#OperationType) to match, in our case ["CREATE"])`
*   `apiGroups (in our case, empty [""])`
*   `apiVersions (in our case, this is ["v1"])`
*   `resources (in our case, this is ["pods"])`

`apiGroups`、`apiVersions`和`resources`都(有点)相互依赖，在这个例子中很简单，因为`Pod`是核心 api 组的一部分，所以不需要指定，空的`[""]`是与核心 api 组匹配的*。*

这里有一个例子:

```
---
apiVersion: admissionregistration.k8s.io/v1beta1
kind: MutatingWebhookConfiguration
metadata:
 name: mutateme
 labels:
 app: mutateme
webhooks:
 - name: mutateme.default.svc.cluster.local
 clientConfig:
 caBundle: ${CA_BUNDLE}
 service:
 name: mutateme
 namespace: default
 path: "/mutate"
 rules:
 - operations: ["CREATE"]
 apiGroups: [""]
 apiVersions: ["v1"]
 resources: ["pods"]
 namespaceSelector:
 matchLabels:
 mutateme: enabled 
```

上面的`${CA_BUNDLE}`指的是从 k8s API 取回的实际 CA bundle，用自己的替换；您可以通过
获得您的集群的 CA 包

```
kubectl config view --raw --minify --flatten -o jsonpath='{.clusters[].cluster.certificate-authority-data}' 
```

由于 webhooks 只能通过 HTTPS (SSL/TLS)调用，您需要为它生成一个新的 SSL 密钥和证书。这样做有点复杂，所以最好来看看这里的: