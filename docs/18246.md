# 你的公司/创业公司真的需要 Kubernetes 吗？

> 原文：<https://dev.to/anortef/do-you-really-need-kubernetes-in-your-company-startup-1ec0>

让我开始说，我喜欢 Kubernetes，我甚至有一个个人的东西，但我是一个经验丰富的 DevOps，这是在我的业余时间。

通常，您希望您的公司使用的技术解决方案的复杂性随着您的需求而增加。

你可能会想:*哇！我需要在我的微服务之间进行自动发现，HTTP API 的负载平衡器，所有东西都必须声明为代码，以便在没有来自 Ops* 的可疑人员的情况下进行维护

当然，你需要这些，尤其是最后一部分，但 Kubernetes 真的是唯一能给你这些的东西吗？

### [码头工─化合物](https://docs.docker.com/compose/)

这是一个用 Python 编写的工具，可以让你使用 YAML 定义环境。

这样做的好处是，您可以使用它来创建和维护开发人员的本地环境，如果您只有一个生产服务器(很少启动)，您甚至可以使用用于开发您的解决方案的相同 YAML 文件来将其部署到服务器中。

不好的一点是，它并没有真正设计成在集群上运行，所以您只能使用一台生产机器。

### [码头工人群](https://docs.docker.com/engine/swarm/)

这是 Docker 自带的一个解决方案，允许将多台机器作为一个集群来管理。

本质上，Swarm 和 Kubernetes 做的是一样的，但并不相同。Swarm 的好处在于，你可以重用与 docker-compose 一起使用的相同的 YAML 文件，并在管道中添加他们在 Swarm 上需要的缩放内容。你可以使用 Traefik 之类的东西来管理访问，如果你以后切换到 Kubernetes，你可以重用它。

唯一的问题是，当达到一个良好的大小时，它缺乏良好的可观测性。

### [Kubernetes](https://kubernetes.io/)

Kubernetes 是一个多面手容器编排器，因此它不处理 Docker 概念，而是处理代表生态系统内部概念的抽象，比如 pod。

Kubernetes 的好处是，使用 Kubernetes 定义(YAML 或 JSON)或 Helm charts(一种关于存储库的部署配置)来管理部署和发布周期非常容易，您甚至可以使用 docker-compose 文件来进行部署。一旦设置正确，它也异常健壮。

缺点是它不是一个容易管理(内部)和部署的软件，因此需要一个 DevOps 团队来处理它。

### 我的结论

这里的关键是要了解你在哪里，如果你仍然在准备一个 MVP 来发布，而你的基础设施中唯一的东西是一个 web 服务器，那么你最好使用 docker-compose 并逐步上升。

此外，如果你的 Kubernetes 将有更多的 orchestrator 服务，而不是正在编排的服务，那么你可能更适合使用 Swarm。

我想做的就是让人们思考他们选择使用的技术，意外的复杂性是有代价的，不应该掉以轻心，所以不要让宣传欺骗了你。