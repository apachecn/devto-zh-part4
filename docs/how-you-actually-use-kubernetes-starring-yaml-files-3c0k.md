# 你实际上是如何使用由 YAML 主演的电影《库伯内特》的

> 原文：<https://dev.to/azure/how-you-actually-use-kubernetes-starring-yaml-files-3c0k>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 我在四篇文章中描述了诸如 Minkube、Pods、节点、服务等概念。尽管从教育的角度来说，你可能会以一种不同于我目前所展示的方式来使用 Kubernetes，你可能会使用 YAML 文件

YAML 文件是让我们定义任何配置的文件。

> YAML 就是 Y . A . M . L .也就是 Y . M . C . A？:)

[![](img/db4506f71536eee49ba71e424c5cfbbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMRoHp2A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pdkhfkeu5sqyngcpt6st.gif)

不，不是那样的，更像是骆驼的 YAML

[![](img/0f5d152e24722fc119b23a854651aebe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7GOH8se2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rq94nuj4sbh9u2eomm1c.jpg)

> 哦，好吧，告诉我更多

YAML 实际上代表了一种语言，尽管它从一开始就代表了另一种语言。改变其含义的原因是为了明确其面向数据的目的，而不是文档标记。

是的，你明白了，这是一个递归定义。它也被称为*人类可读数据序列化语言*。

> 感谢历史课，那它在 Kubernetes 的作用呢？

正如我们之前所说的，我们已经通过使用命令行向您展示了 Kubernetes 的用法，这并不是人们使用它的方式。

> 让我猜猜我用了 YAML 的文件

没错，你可以描述和部署到 Kubernetes 的每一个东西都可以被描述为一个 YAML 文件。实际上这样也更简单。但是在我们谈论 Kubernetes 的具体用法之前，我们需要知道一些关于 YAML 文件的基础知识，以便在我们看文件时理解它

> 我需要咖啡来做这个，对吗？

## 资源

这篇文章实际上不是一个系列的一部分，但我已经写了它的其他部分。如果你完全是新手，我建议你先阅读下面的第一部分

*   [第一部分——从第一部分开始，基础知识、部署和 Minikube](https://dev.to/azure/kubernetes-from-the-beginning-part-i-4ifd) 在这一部分中，我们将介绍 Kubernetes 的原因、一些历史和一些基本概念，如部署、节点、单元。
*   [第二部分-介绍服务和标签](https://dev.to/azure/kubernetes-part-ii-revisiting-pods-and-nodes-and-introducing-services-and-labeling-5fi7)在这一部分，我们将加深对 pod 和节点的了解。我们还引入了服务和标签，使用标签来查询我们的工件。
*   [第三部分-缩放](https://dev.to/azure/kubernetes-part-iii-scaling-1mmi)
    在这一部分中，我们来看看使用*期望的*状态的缩放。我们指定我们想要多少个豆荚，让 Kubernetes 来做繁重的工作，以确保您的豆荚扩大到所需的数量，并通过使用一种叫做自我修复的东西来维护它。

*   [第四部分-自动缩放](https://dev.to/azure/kubernetes-from-the-beginning-part-iv-autoscaling-54l6)
    这就是你在 Kubernetes 中通过查看 CPU 使用情况来执行缩放的方式

以下是我推荐你在 Kubernetes 开始点击时查看的更多资源:

*   [水平 Pod 自动缩放](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)这详细描述了一切如何工作。在流程和 API 级别上
*   如果你想试用 AKS，Azure Kubernetes 服务，你需要一个免费的 Azure 账户
*   了解 Kubernetes 的最佳资源之一是谷歌的这个官方网站。
*   [Kubernetes 概述](https://azure.microsoft.com/en-gb/topic/kubernetes/?wt.mc_id=devto-blog-chnoring)Kubernetes、其所有部件及其工作原理的概述
*   [云中的 Kubernetes](https://azure.microsoft.com/en-gb/services/kubernetes-service/?wt.mc_id=devto-blog-chnoring)你是否觉得自己已经了解了 Kubernetes 的一切，只是想学习如何使用托管服务？那么这个链接是给你的
*   [AK 上的文件，azure kublers 服务](https://docs.microsoft.com/en-gb/azure/aks/?wt.mc_id=devto-blog-chnoring)azure kublers 服务，一种托管 kublers 服务
*   [AKS 最佳实践](https://docs.microsoft.com/en-us/azure/aks/best-practices?wt.mc_id=devto-blog-chnoring)您已经了解 AKS，并想学习如何更好地使用它？

## YAML 格式

为了能够使用这种格式，我们需要了解什么？YAML 是 JSON 的超集，这使得 JSON 文件成为有效的 YAML 文件。这是一条有趣的信息，但真正想知道的是其中使用的结构，即:

*   地图
*   列表

> 好，给我看看

### 地图

先来创建一个文件，姑且称之为`config.yaml` :

```
--- 
version: 1
name: some value 
```

这上面包含了一些关于 YAML 格式的有用见解。`---`被称为分隔符，是可选的，除非我们在文件中有多个结构。之后我们看到的是`version`和`name`。键由它的值用`:`分隔。

> 这应该是 JSON 的超集吧，我们是不是漏了双引号？

啊，眼尖，双引号`""`其实是可选的。为什么要写得太多呢？所以上面对应的是 JSON 里的下面:

```
{  "version":  "1",  "name":  "some value"  } 
```

上面是一张地图，但是你的地图中有一个键可以反过来指向一张地图，就像下面这个例子:

```
--- 
version: 1
name: some value,
address: 
  street: One Microsoft Way
  city: Redmond
  country: USA 
```

### 列表

好吧，那么，让我们看看我们的下一个格式列表。考虑下面的例子:

```
version: 1
todos:
 - shop
 - clean
 - do math
 - write essay 
```

在这种情况下，`todos`是列表类型，这将对应于 JSON 中的以下内容:

```
"version":  "1",  "todos":  ["shop",  "clean",  "do match",  "write essay"] 
```

> 作为地图的列表项呢？

很简单:

```
created: 2019-01-01
items:
 - quantity: 2
   price: 10
   discounts:
     - id: 1
       displayName: 10% off  
     - id: 111
       displayName: Holiday Sale 
```

上述内容将对应于以下 JSON:

```
{  "created":  "2019-01-01",  "items":  [{  "quantity":  2,  "price"  :  111,  "discounts":  [{  "id":  1,  "displayName":  "10% off"  },  {  "id":  111,  "displayName":  "Holiday Sale"  }]  }]  } 
```

> 我想我明白了。Kubernetes 怎么样？

哦，还有一件事，在我们把所有 Kubernetes。标签=不酷。空格是你所使用的，但是*要一致*，如果你开始使用两个空格，那就一直使用。

## YAML 在库伯内

在 Kubernetes 中，可以部署到集群的大多数东西都可以描述为 YAML 文件。这有一些明显的优势:

*   **保存您的手指**:不再需要在命令行上输入长结构
*   源代码控制:YAML 文件可以存储在源代码控制中，因此我们可以跟踪变更
*   灵活性:你可以使用 YAML 创建比命令行更复杂的结构

那么我们在 YAML 要描述什么呢？我们将不仅描述而且部署以下内容:

*   吊舱是最小的可展开单元。我知道，你通常不会只部署一个吊舱，为了练习起见，我们就做这一次
*   **部署**，这更像是我们想要部署的级别。它让我们能够定义*副本*以及其他东西

### 描述和部署 Pod

让我们逐步为 Pod 建立 YAML 文件，最后部署到集群。好，我们开始:

```
—--
apiVersion: v1
kind: Pod 
```

我们从指定一个`apiVersion`开始。然后我们继续指定`kind: Pod`。现在这个很重要。这可以是多种不同的类型，例如部署或服务。

**元数据**
我们文件的下一部分是元数据部分。这里我们给它一个名字和一个标签:

```
metadata:
 name: My Pod
 labels:
   app: web 
```

**规格**

现在我们到了描述组成 Pod 的部件的部分。

```
spec:
  containers:
    - name: first-app
      image: gcr.io/google-samples/kubernetes-bootcamp:v1
      ports:
        - containerPort: 8080 
```

我们的`pod.yaml`文件现在看起来应该是这样的:

```
apiVersion: v1
kind: Pod
metadata:
 name: my-pod
 labels:
   app: web
spec:
  containers:
    - name: first-app
      image: gcr.io/google-samples/kubernetes-bootcamp:v1
      ports:
        - containerPort: 8080 
```

### 部署我们的吊舱 YAML

此时，我们希望将其部署到集群中。所以我们会再和老朋友，`minikube`和`kubectl`聊聊。

有时候我的`minikube`有点慢，所以我用命令`minikube start`重启它。一旦该命令运行完毕，让我们用
来检查 pod 的情况

```
kubectl get pods 
```

它应该列出当前启动并运行的所有单元:

[![](img/cdabd8e52b11a2b3b0813db85dec1b0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aYZLiY35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5y2719fm1ca6y1q7b1uv.png)

正如你在上面看到的，我们在过去已经做了相当多的实验，创造了各种各样的豆荚，这对你来说显然是不同的。

此后，让我们使用我们的`pod.yaml`文件并从它创建一个 Pod。为此，我们运行命令:

```
kubectl create -f pod.yaml 
```

这仅仅意味着从文件名为`pod.yaml`的文件`-f`创建一个 Pod。该命令的结果是:

[![](img/ef62818ac56d6866985498c1258cce34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--338XzTW---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fmwzj23hbfjk8hifhzxg.png)

该命令的结果暗示我们创建了一个 Pod，让我们重新运行 Pod 列表命令:

```
kubectl get pods 
```

[![](img/ebf2cbda9ed5c24dcd740f2c3f3d546a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iwoJcy9a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wmyzv2sjt7i32l6ul5yf.png)

这一次，我们的新吊舱已经启动并运行。但是当然，仅仅部署这样一个吊舱是不行的。原因是豆荚是会死的，如果它倒下了，它就没了。没有服务会再提起。

> 那为什么还要展示出来呢？

展示如何从一个文件创建一个工件可能是有教育意义的，但是从 Kubernetes 的角度来看，它并没有使用所有精彩的服务来保持它的活力。毕竟，这就是我们有 Kubernetes 的原因，为了弹性。

因此，接下来我们来看一个部署。

在我们继续之前，让我们用
来清理我们自己

```
kubectl delete pods my-pod 
```

这触发了一种叫做*优雅*的终止，这是推荐的做事方式。如果你不耐烦，你可以用下面的命令*强制删除*:

```
kubectl delete pods my-pod --grace-period=0 --force 
```

我们当然删除了*优美的*方式，所以过了一会儿我们得到这个:

[![](img/99ebef7e763d8cf0e4405740aea6f583.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rNekLQfl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uyqfkrhfq77dnux662mc.png)

如果你跑了，我们的逃生舱就没了。我们所有的应用程序数据都没了。

> 我明白了，放轻松，我不会直接创建豆荚，可以继续吗？

### 部署

好吧，那么，部署，正确的做事方式。

我们将使用一种叫做`ReplicationController`的东西。现在，这在技术上是一个更老的概念，逐渐被管理`ReplicaSets`部署的高级概念`Deployment`所取代。在仪表板支持和其他事情上仍然存在问题，这使得所有这些类似的概念让我们更加困惑；)底线是在一段时间内使用这些旧的概念是可以的。

这一次我们将定义一种新的对象类型，即所谓的`ReplicationController`。现在，这是一个部署，它不仅指出将什么映像部署到一个单元，而且指出我们在任何给定点需要多少个单元，即所谓的*期望状态*。我们来看看文件，从最上面开始:

```
apiVersion: v1
kind: ReplicationController
metadata: 
  name: my-rc 
```

我们将`kind`定义为类型`ReplicationController`而不是`Pod`，并在元数据中将其命名为`my-rc`。现在让我们进入规范部分:

```
spec:
  replicas: 5
  selector: 
    app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: my-first-app
        image: gcr.io/google-samples/kubernetes-bootcamp:v1
        ports:
        - containerPort: 8080 
```

这里真正有趣的部分是`replicas`，豆荚的数量和它的其余部分主要是我们再次指出相同的图像。

该规范在顶层看起来略有不同，因为它是一个复制控制器，但内部的`spec`是我们所习惯的，指出了映像、端口和其他东西。让我们看看我们命名为`rc.yaml` :
的完整文件

```
apiVersion: v1
kind: ReplicationController
metadata: 
  name: my-rc
spec:
  replicas: 5
  selector: 
    app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: my-first-app
        image: gcr.io/google-samples/kubernetes-bootcamp:v1
        ports:
        - containerPort: 8080 
```

我们通过键入:
来基于文件创建上面的部署

```
kubectl create -f rc.yaml 
```

[![](img/147670b51abbb7051e18a1a96aef091b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0rjmgfEU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/njz9cljlm2z5ff9bezr7.png)

上面我们看到我们的复制控制器被创建。

让我们现在列出我们所有的 pod，并确保我们得到的 pod 与预期的一样多:

```
kubectl get pods 
```

[![](img/6190a994d6f98b310a72755d4653b77c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---lULYaLy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/arw9v572xiwtf2rw7u8q.png)

查看上面的清单，我们看到我们有 5 个副本，5 个 Pod 实例。我们可以试着删除一个，看看会发生什么:

```
kubectl delete pods my-rc-wx6h7 
```

它告诉我们上面的 Pod 已被删除，因此我们等待*优雅的*删除完成，然后我们再次列出我们的 Pod，这是我们得到的结果:

[![](img/e3bfaf4f8d2cf687f8c3cc2b3c99eea3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qWeE2bha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/co75bqrdimib6rxr3nic.png)

正如您在上面看到的，我们的特定 Pod 被删除了，但它被另一个 Pod 取代，副本的数量保持不变，Kubernetes 正在完成它的工作

> 我们怎么把它弄下来，大蒜，木桩？

[![](img/c947d2fd6810ebe05ef6218cc210ee56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L7XLqJb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ri61sug1nj24zauf9g8b.gif)

让我们从列出所有复制控制器开始:

```
kubernetes get replicationcontroller 
```

[![](img/59dfadd3bcf776c73249b13e395e85b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_hkjQomt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2c9yjfyityk9v4bnsdov.png)

现在有两种方法可以降低它，大蒜或删除；)

> 是啊是啊很搞笑，删了，多给我讲讲*无聊*

就像输入
一样简单

```
kubectl delete replicationcontroller [name of replication controller] 
```

如果您执行上述操作，将会发生的情况是，它不仅会删除复制控制器，还会删除所有的 pod，即所谓的级联删除。如果您只是希望复制控制器消失，您可以指定`--cascade=false`。我们希望它全部消失，所以我们只需键入:

```
kubectl delete my-rc 
```

这应该能清除所有的东西。如果我们运行`kubectl get replicationcontroller`它应该消失了。让我们运行`kubectl get pods`并确保豆荚也不见了。

让我们也去掉另一个复制控制器，它在我的系统中已经存在太久了:

```
kubectl delete replicationcontroller my-rc 
```

快速命中:

```
kubectl get pods 
```

你将会看到豆荚被终结:

[![](img/8513edcdfcf3acc368202d85aa780236.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ZB66M1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43bpff6te1w67zo1hr9y.png)

## 总结

在这篇文章中，我们谈到了文件格式 YAML，它是什么，它是如何工作的。我们还讨论了应该如何使用 YAML 文件来配置我们的 Kubernetes 体验。此外，我们还展示了如何部署 pod 以及`ReplicationControllers`。希望这是有教育意义的，让你体验一下真正的 Kubernetes 管理是什么样子的。