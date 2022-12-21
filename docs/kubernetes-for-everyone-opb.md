# 为大家干杯💡🎉

> 原文：<https://dev.to/sendilkumarn/kubernetes-for-everyone-opb>

# Kubernetes

Kubernetes 是运行容器化应用程序的标准。

> Kubernetes (K8s)是一个针对容器化应用的`automating deployment`、`scaling`和`management`的开源系统。

[![](img/be636842c82bb87139cff50f9f9aebc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--abAth0MU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/idtqaj43wbzw5704fwdb.png)

Kubernetes 使部署和运行容器化的应用程序变得容易。Kubernetes 使用简单。

Kubernetes 很难理解，因为它提供了大量选项来简化您的部署。

顾名思义，Kubernetes 是一个领航员(或)舵手，帮助你航行的集装箱世界。Kubernetes 是社区为社区构建的可移植、可扩展的系统。

正如凯尔西正确引用的

> Kubernetes 可以做最好的系统管理员会做的事情:自动化、故障转移、集中日志记录和监控。它采用了我们在 DevOps 社区中学到的知识，并将其默认为开箱即用。

为了与 Kubernetes 合作，了解以下内容非常重要

*   多么不可思议的作品？
*   Kubernetes 是如何构建的？
*   Kubernetes 中的各种成分是什么？

让我们开始黑 Kubernetes。

## Kubernetes 是如何工作的？

Kubernetes 以高度可用的集群模式运行。每个 Kubernetes 集群由一个或多个主节点和几个工作节点组成。

[![Alt Text](img/ca9121fe88656acd30dcb0c5d5ab5f83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KdhR6bGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/gkc90lkkd9cdfqjx2i2m.png)

#### 主节点

主节点由 API 服务器、调度程序、控制器等组成。这个节点被称为 Kubernetes 的`control plane`。这个控制平面就是 Kubernetes 的`brain`。

也就是说，控制平面负责 Kubernetes 内部的所有操作。

[![Alt Text](img/88c5e5b1cda53c29ecb335ce69506c36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i1t8r2LU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/wgenne5f9y8a25shgb29.png)

通过`API server`，我们可以指示库伯内特人或者从库伯内特人那里获得信息。

`Scheduler`负责调度吊舱。

`controllers`负责运行资源控制器。

`etcd`是库伯内特人的储藏室。它是键值存储。

#### 节点

[![Alt Text](img/9ba1cc30970ea63dfe223f0d47bcfd2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xcWH2nLa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/g9w3a7g1ytmxz83m46n3.png)

工作者节点有一个 Kubelet 和代理。

Kubelets 是实际的主力，Kube-proxy 处理网络。

#### 工作

[![Alt Text](img/b5614442db01d03662b94bdaa59238a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IlxAOYSR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/h0hxgch8ape716dwxl0t.png)

我们通过`kubectl apply`命令向 Kubernetes 集群提供`yaml`文件。

`apply`命令调用 API 服务器，API 服务器将信息发送给`controller`，同时将信息存储到`etcd`。

然后,`etcd`在多个节点上复制这些信息，以便在任何节点出现故障时都能存活。

`controller`将检查给定状态是否与期望状态匹配。如果不是，它通过向`scheduler`发送信息来启动吊舱部署

这些检查被称为在 Kubernetes 内部运行的**协调循环**。这个循环的工作是验证所请求的状态是否被正确维护。如果预期状态和实际状态不匹配，该循环将执行必要的操作，将实际状态转换为预期状态。

`scheduler`里面有一个队列。一旦队列中收到消息。

然后,`scheduler`将调用 kubelet 执行预期的操作，比如部署容器。

这是 Kubernetes 如何部署的 10000 英尺鸟瞰图。

库伯内特斯有各种各样的成分。让我们看看它们是什么，它们有什么用处。

## 库伯内特斯的成分

### 豆荚

> 一般来说，豆荚只不过是一群海豚或鲸鱼。

[![](img/0014848c1e63b8c8179d61c8526a397d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6dcWD7cK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wri3njxsoku0z6na9zj8.png)

同样，在 Kubernetes 的世界里，`pods`是一群生活在一起的容器。一个 pod 可能包含一个或多个容器。

`pod`是 Kubernetes 最小的部署单位。通常，不能生活在另一个容器的范围之外的容器被分组以形成一个 pod。

这就是你在 Kubernetes 中定义 pod 的方式。

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo  Hello  Kubernetes!  &&  sleep  3600'] 
```

Enter fullscreen mode Exit fullscreen mode

*   *apiVersion* 表示 Kubernetes 集群在解析和执行这个文件时使用哪个版本的 API。
*   *种类*定义了库本内特对象的**种类**，该文件将引用。
*   *元数据*包括识别 Pod 所需的所有元数据。
*   *规格*包括集装箱信息。

### 部署

而豆荚是部署单位。为了让应用程序工作，它需要一个或多个 pod。Kubernetes 认为这一整套都是部署。

这样部署就记录了关于吊舱的信息。Kubernetes 使用这些部署信息来管理和监控部署在其中的应用程序。

下面的文件是样例部署文件，它告诉 Kubernetes 使用`nginx:1.7.9`容器创建一个`nginx`的部署。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80 
```

Enter fullscreen mode Exit fullscreen mode

### 复制集

而部署告诉 Kubernetes 您的应用程序需要哪些容器以及要运行多少副本。`replica sets`是确保这些副本正常运行的工具。

ReplicaSet 负责管理和监控副本。

### statefullset

我们经常需要永久存储或永久网络标识符，或者有序部署、扩展和更新。在这期间，我们将使用`StatefulSets`。

您可以如下定义 StatefulSet】

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi 
```

Enter fullscreen mode Exit fullscreen mode

我们装载了卷，并声明了卷存储。

### DaemonSet

有时，您需要在 Kubernetes 集群的每个节点上运行一个 pod。例如，如果您从每个节点收集指标，那么我们将需要在收集指标的每个节点上安排一些 pod。我们可以对这些节点使用 DaemonSet。

### 服务

部署定义了容器上运行的应用程序的实际状态。用户将需要访问应用程序，或者您可能需要连接到容器来调试它。服务将帮助你。

服务是 Kubernetes 对象，它提供了从外部世界或在它们之间对容器的访问。

我们可以如下定义服务:

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376 
```

Enter fullscreen mode Exit fullscreen mode

上面的`service`将端口`80`上的传入连接映射到目标端口`9376`。

> 在 Kubernetes 的世界中，您可以将服务视为负载平衡器、代理或流量路由器。

### 联网

这是 Kubernetes 最重要的元素。正在运行的 pod 应该暴露在网络中。在 pod 内部运行的容器应该在它们之间以及与外部世界进行通信。

虽然服务提供了连接到 pod 的方法，但是网络决定了如何公开这些服务。

在 Kubernetes 中，我们可以通过以下方式公开服务:

*   负载平衡
    *   负载平衡器提供了一个外部 IP，通过它我们可以访问内部运行的 pod。
    *   Kubernetes 将启动服务，然后`asynchronously`启动一个负载均衡器。

[![](img/5274ac15f915101d4428dd97e0241ab5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ga9dtoD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29miv7gkc1bmpz0yfdze.gif)

*   节点端口
    *   每个服务都有一个动态分配的端口。
    *   我们可以使用 Kubernetes 主 IP 访问服务。

[![](img/a29b73e1e24df36fc6e74ac797c302d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5-WFXc6N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pactfmoby255r4pny8iq.gif)

*   进入
    *   每个服务都有一个单独的地址。
    *   这些服务然后由入口控制器访问。
    *   入口控制器不是公共 IP 或外部 IP。

[![](img/7c0f06c1948a7d66817343053046a8b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PxM64GHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/09tndignuksgqrjdqlze.gif)

### 秘密

通常对于应用程序，我们需要提供密码、令牌等。，Kubernetes 提供了`secrets`对象来存储和管理敏感信息。我们可以创建一个如下的秘密:

```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
stringData:
  config.yaml: |-
    apiUrl: "https://my.api.com/api/v1"
    username: {{username}}
    password: {{password}} 
```

Enter fullscreen mode Exit fullscreen mode

## 最佳实践

> 而 Kubernetes 是一个海洋，我们所看到的只是其中的一滴水。由于 Kubernetes 支持广泛的应用程序和选项，因此有各种不同的选项和功能可用。

使用 Kubernetes 时可以遵循的一些最佳实践是:

#### 让 YAML 变小

`yaml`文件是 Kubernetes 配置的核心。

我们可以在一个`yaml`中定义多个 Kubernetes 配置。而`yaml`与`JSON`相比减少了样板文件。但是`yaml`文件仍然对空间敏感并且容易出错。

所以总是尽量减小`yaml`文件的大小。

对于每个服务，部署、秘密和其他 Kubernetes 对象都在一个单独的`yaml`文件中定义它们。

> 将你的 yaml 文件分割成更小的文件。

`single responsibility principle`在这里适用。

#### 图像启动时间更小更快

Kubernetes 会在系统崩溃、升级或使用量增加时自动重启 pod。让映像有更快的启动时间是很重要的。为了有更快的启动时间，我们需要更小的映像。

高山影像是你的朋友。使用 Alpine 映像作为基础，然后仅在绝对必要时向映像添加组件或库。

> 永远记住使用较小的图像尺寸。使用`builder pattern`从 Alpine 图像创建图像。

#### 健康-僵尸过程

Docker 容器只有在容器内运行的所有进程都终止时才会终止。即使其中一个进程被终止，Docker 容器也会返回`healthy`状态。这就创建了一个`Healthy-Zombie`流程。

尝试在容器中有一个单一的进程。如果无法运行单个进程，那么尝试使用一种机制来判断是否所有需要的进程都在运行。

#### 清理未使用的资源

在容器世界中，未使用的资源占用内存是很常见的。确保资源得到正确清理非常重要。

#### 想想要求&限制

确保为所有容器正确指定要求和限制。

```
resources:
    requests:
        memory: "100Mi"
        cpu: "100m"
    limits:
        memory: "200Mi"
        cpu: "500m" 
```

Enter fullscreen mode Exit fullscreen mode

`requests`是容器保证得到的限制。`limits` are 是允许容器使用的最大或最小资源。

> pod 中的每个容器都可以请求和限制它们的资源。

#### 红色/使用图案

使用`RED`模式监控和管理您的服务。

*   要求
*   错误
*   持续时间

跟踪请求、响应中的错误以及接收响应的持续时间。根据这些信息，调整您的服务以获得最佳性能。

对于资源，使用`USE`模式。

*   利用
*   浸透
*   错误

监控资源利用率和资源饱和程度，以及有哪些错误。根据这些信息，调整您的资源以优化资源分配。

希望这能让你对`Kubernetes`有一个简要的了解。点击 [kubernetes.io](https://kubernetes.io) 获取更多关于 kubernetes 的信息。

现在，您希望在 Kubernetes 上部署一个示例应用程序，并使用 Istio 来查看这篇文章。

你可以在推特上关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️