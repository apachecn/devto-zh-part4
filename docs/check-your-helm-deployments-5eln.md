# 检查你的头盔部署！

> 原文：<https://dev.to/lepovirta/check-your-helm-deployments-5eln>

部署资源是 Kubernetes 中处理应用程序部署的实际方式，但是有许多工具可以管理它们。安全管理它们的一种方法是直接使用 kubectl，正如我在上一篇文章中所展示的。

将资源部署到 Kubernetes 的另一种流行方式是使用 Kubernetes 的包管理器 [Helm。在本文中，我将讨论如何使用 Helm 重复前一篇文章中演示的部署模式。我们将使用 Helm 版本 2.14.2 进行演示。](https://helm.sh/)

这篇文章最初发布在[极地小分队的博客](https://medium.com/polarsquad)上。

## 示例舵图

在 Helm 中，Kubernetes 资源以[图表](https://v2.helm.sh/docs/developing_charts/#charts)的形式发布:YAML 或 JSON 格式的模板化 Kubernetes 资源集合。可以从外部 Helm 存储库、图表归档文件或本地图表目录部署图表。每个图表都有自己的一组变量，可用于定制部署。让我们生成一个本地目录的导航图，我们可以用它来测试失败和成功的部署。

```
$ helm create demo 
```

Enter fullscreen mode Exit fullscreen mode

这将在目录`demo/`中创建一个简单的图表，其中包含一个 web 服务器的部署。路径`demo/templates/deployment.yaml`中的模板生成部署清单。让我们对就绪探测器进行参数化，以便我们可以通过更改舵图参数来模拟失败的部署。

```
readinessProbe:
  httpGet:
    path: {{ .Values.readinessPath | default “/” }}
    port: http 
```

Enter fullscreen mode Exit fullscreen mode

## 未检查部署

使用 Helm CLI 安装 Helm 图表有两种方式: [`helm install`](https://v2.helm.sh/docs/helm/#helm-install) 和 [`helm upgrade --install`](https://v2.helm.sh/docs/helm/#helm-upgrade) 。install 子命令总是安装一个全新的图表，而 upgrade 子命令可以升级现有的图表并安装一个新的图表，如果以前没有安装过该图表的话。有了升级功能，我们可以使用一个命令进行安装和升级，这对于自动化来说非常方便。让我们用它来安装我们之前创建的演示舵图表。

```
$ helm upgrade --install demo demo/
Release "demo" does not exist. Installing it now.
NAME:   demo
LAST DEPLOYED: Fri Aug 16 13:48:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Deployment
NAME  READY  UP-TO-DATE  AVAILABLE   AGE
demo  0/1    1           0           1s

==> v1/Pod(related)
NAME                     READY  STATUS               RESTARTS  AGE
demo-69c7467798-84nr9    0/1    ContainerCreating    0         1s

==> v1/Service
NAME  TYPE        CLUSTER-IP    EXTERNAL-IP  PORT(S)  AGE
demo  ClusterIP   10.96.196.73  <none>       80/TCP   1s 
```

Enter fullscreen mode Exit fullscreen mode

正如我们从输出中看到的，已经安装了舵图，但是部署仍在进行中。赫尔姆没有检查我们的部署是否成功完成。

当我们创建一个失败的部署时，我们应该会看到相同的结果。让我们通过将就绪探测器的路径更改为我们知道不起作用的路径来故意中断部署。

```
$ helm upgrade --install --set readinessPath=/fail demo demo/
Release "demo" does not exist. Installing it now.
NAME:   demo
LAST DEPLOYED: Fri Aug 16 13:53:26 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Deployment
NAME  READY  UP-TO-DATE  AVAILABLE   AGE
demo  0/1    1           0           5m

==> v1/Pod(related)
NAME                   READY  STATUS               RESTARTS  AGE
demo-54df8f97bb-ffp4b  0/1    ContainerCreating    0          0s
demo-69c7467798-84nr9  1/1    Running              0          5m
==> v1/Service
NAME  TYPE        CLUSTER-IP    EXTERNAL-IP  PORT(S)  AGE
demo  ClusterIP   10.96.196.73  <none>       80/TCP   5m 
```

Enter fullscreen mode Exit fullscreen mode

输出显示图表已经“部署”，但是更新的 Pod 没有成功启动。我们可以通过查看部署展示状态来验证部署没有成功完成。

```
$ kubectl rollout status deployment demo
Waiting for deployment "demo" rollout to finish: 1 old replicas are pending termination... 
```

Enter fullscreen mode Exit fullscreen mode

然而，图表部署[历史](https://v2.helm.sh/docs/helm/#helm-history)将显示第一次部署被第二次部署取代。

```
$ helm history demo
REVISION  STATUS      DESCRIPTION     
1         SUPERSEDED  Install complete
2         DEPLOYED    Upgrade complete 
```

Enter fullscreen mode Exit fullscreen mode

让我们删除图表，重新开始。

```
$ helm delete --purge demo 
```

Enter fullscreen mode Exit fullscreen mode

## 等待和超时

似乎 Helm chart 部署的工作方式类似于`kubectl apply`的工作方式:资源被创建，但是实际的部署没有被验证。有了 kubectl，我们可以使用`kubectl rollout status`来进一步检查部署的状态。那么，在这种情况下，舵的等效物是什么呢？

Helm 安装和升级命令包括两个 CLI 选项来帮助检查部署:`--wait`和`--timeout`。当使用`--wait`时，Helm 将等待直到部署中的最小期望数量的吊舱被发射，然后将释放标记为成功。只要用`--timeout`设定，舵就会等待。默认情况下，超时设置为 300 秒。我们来试试吧！

```
$ helm upgrade --install --wait --timeout 20 demo demo/
Release "demo" does not exist. Installing it now.
NAME:   demo
LAST DEPLOYED: Fri Aug 16 15:47:10 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Deployment
NAME  READY  UP-TO-DATE  AVAILABLE  AGE
demo  1/1    1           1          8s

==> v1/Pod(related)
NAME                   READY  STATUS   RESTARTS  AGE
demo-69c7467798-4tkqf  1/1    Running  0         8s

==> v1/Service
NAME  TYPE       CLUSTER-IP      EXTERNAL-IP  PORT(S)  AGE
demo  ClusterIP  10.102.127.162  <none>       80/TCP   8s 
```

Enter fullscreen mode Exit fullscreen mode

部署按预期成功完成。让我们看看当我们在失败的部署中尝试时会发生什么。

```
$ helm upgrade --install --wait --timeout 20 --set readinessPath=/fail demo demo/
UPGRADE FAILED
Error: timed out waiting for the condition
Error: UPGRADE FAILED: timed out waiting for the condition 
```

Enter fullscreen mode Exit fullscreen mode

非常好！我们终于从一次失败的部署中得到反馈。我们也可以在掌舵图表历史中看到这一点。

```
$ helm history demo
REVISION  STATUS     DESCRIPTION
1         DEPLOYED   Install complete
2         FAILED     Upgrade "demo" failed: timed out waiting for the condition 
```

Enter fullscreen mode Exit fullscreen mode

## 手动回滚

现在我们有一个失败的升级，您可能认为您可以只部署图表的先前版本就可以了。不幸的是，这并不能让您回到工作版本。

```
$ helm upgrade --install --wait --timeout 20 demo demo/
UPGRADE FAILED
Error: timed out waiting for the condition
Error: UPGRADE FAILED: timed out waiting for the condition
$ helm history demo
REVISION  STATUS     DESCRIPTION
1         DEPLOYED   Install complete
2         FAILED     Upgrade "demo" failed: timed out waiting for the condition
3         FAILED     Upgrade "demo" failed: timed out waiting for the condition 
```

Enter fullscreen mode Exit fullscreen mode

我不确定为什么会这样，但情况会变得更糟！如果您尝试对您的图表进行另一次更新，也会失败。

```
$ helm upgrade --install --wait --timeout 20 --set replicaCount=2 demo demo/
UPGRADE FAILED
Error: timed out waiting for the condition
Error: UPGRADE FAILED: timed out waiting for the condition 
```

Enter fullscreen mode Exit fullscreen mode

我能想到的摆脱这种情况的唯一方法是完全删除图表部署并重新开始。

```
$ helm delete --purge demo 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 [`helm rollback`](https://v2.helm.sh/docs/helm/#helm-rollback) ，而不是尝试使用升级命令。它是专门为推出您以前部署过的图表版本而设计的。要使用 rollback 子命令，我们需要向它提供回滚到的版本。它还接受与安装和升级相同的等待和超时选项，我们可以使用这些选项来验证回滚本身是否成功。请注意，回滚不能用于从上述情况中恢复。让我们回到第一次修订。

```
$ helm upgrade --install --wait --timeout 20 demo demo/
$ helm upgrade --install --wait --timeout 20 --set readinessPath=/fail demo demo/
$ helm rollback --wait --timeout 20 demo 1
Rollback was a success. 
```

Enter fullscreen mode Exit fullscreen mode

厉害！同样，这也应该可以在舵图表历史中看到。

```
$ helm history demo
REVISION  STATUS       DESCRIPTION
1         SUPERSEDED   Install complete
2         SUPERSEDED   Upgrade "demo" failed: timed out waiting for the condition
3         DEPLOYED     Rollback to 1 
```

Enter fullscreen mode Exit fullscreen mode

## 使用原子的自动回滚

以这种方式自动化部署和回滚有点麻烦，因为您需要弄清楚如何从 Helm 历史中解析最后一次成功的修订，以便您可以发出回滚。在 history 命令中使用`-o json`选项，可以获得 JSON 格式的历史记录，这应该会有所帮助。然而，有一条捷径可以避免这一切。

Helm 安装和升级命令包括一个`--atomic` CLI 选项，该选项将导致图表部署在失败时自动回滚。启用原子选项将自动启用等待。我们试试吧！

```
$ helm upgrade --install --atomic --timeout 20 --set readinessPath=/fail demo demo/
UPGRADE FAILED
Error: timed out waiting for the condition
ROLLING BACKRollback was a success.
Error: UPGRADE FAILED: timed out waiting for the condition
$ helm history demo
REVISION  STATUS       DESCRIPTION
1         SUPERSEDED   Install complete
2         SUPERSEDED   Upgrade "demo" failed: timed out waiting for the condition
3         SUPERSEDED   Rollback to 1
4         SUPERSEDED   Upgrade "demo" failed: timed out waiting for the condition
5         DEPLOYED     Rollback to 3 
```

Enter fullscreen mode Exit fullscreen mode

完美！这也将工作，然后第一次安装图表失败。如果没有可恢复的修订，图表部署将被删除。

```
$ helm delete --purge demo
release "demo" deleted
$ helm upgrade --install --atomic --timeout 20 --set readinessPath=/fail demo demo/
Release "demo" does not exist. Installing it now.
INSTALL FAILED
PURGING CHART
Error: release demo failed: timed out waiting for the condition
Successfully purged a chart!
Error: release demo failed: timed out waiting for the condition
$ helm history demo
Error: release: "demo" not found 
```

Enter fullscreen mode Exit fullscreen mode

## 掌舵笨拙

Helm 支持开箱检查部署和自动回滚，这一点很棒，但是与传统的基于 kubectl 的部署相比，它有几个警告。

首先，没有等待部署完成的官方命令，这与类似于`kubectl rollout status`的安装和升级过程是分开的。在您怀疑同一舵图上的部署可能正在进行的情况下，此功能非常有用:您可以等待现有部署完成，然后再尝试应用您的更改。但是，可以通过创建一个脚本来解决这个问题，该脚本使用`helm status`子命令持续轮询图表部署的状态。

其次，图表中所有资源的部署超时都是全局的。将其与部署中的 [`progressDeadlineSeconds`功能进行比较，后者允许估计每个 Pod 的超时。在 Helm 中，您需要在一次超时中考虑部署中的所有 pod。这使得估计图表部署的正确超时更加困难。如果您对它的估计过低，您会得到过早失败的部署，即使它仍然可以取得进展。如果估计过高，图表部署将不得不等待很长时间才能注意到部署没有任何进展。](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#progress-deadline-seconds)

## 结论

在本文中，我演示了如何安全地部署包含 Kubernetes 部署和自动回滚的 Helm 图表。我还谈到了 Helm 监控部署健康性的方法中固有的警告。我还没有涉及的一个领域是如何使用包含不仅仅基于 Kubernetes 部署资源的资源的 Helm 图表来处理安全部署。

感谢阅读！