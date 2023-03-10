# 如何把一个 django app 放到云端？

> 原文：<https://dev.to/fergyfresh/how-to-put-a-django-app-in-the-cloud-2clg>

# GKE 集群和 CloudSQL，一个传奇

尤普，我说佐贺。所以，如果你想用 Django 和 CloudSQL (postgres)后端创建一个 GKE 集群，请耐心等待。

好吧，所以你在这里读这篇文章，所以我假设你正在与这个作斗争。

以下是这篇文章的先决条件:

*   你有 django 应用程序
*   您可以构建该应用程序的容器
*   您可以使用 postgresql 作为 sql 后端

如果你没有，你可以用这个

## 在谷歌云上设置项目。

### 创建项目

在设置项目之前，我们需要做的第一件事是创建一个项目，以便对所有云组件进行分组。

在屏幕左上角创建帐户后，您可以在[https://cloud.console.google.com](https://cloud.console.google.com)创建一个项目。见下面截图:

[![Create Project](img/0b640ad8131d1d4361254c1cbb669d19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BP7V9Fsc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fergyfresh/fergyfresh.github.io/master/assets/gke-blog/create-project.png)

### 创建 CloudSQL 实例

创建一个带有私有 IP 的 postgres CloudSQL，因为这是我们在将 GKE 连接到 CloudSQL 时需要的，从技术上来说，你可以用它作为公共 IP 来做这件事，但是你需要在 django 容器内部运行一个`cloud_sql_proxy`,所以我们现在不讨论这个，这实际上更容易。

[![Create CloudSQL](img/5b55aadecf7fc55cef9092ac482842ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m1uJu8bu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fergyfresh/fergyfresh.github.io/master/assets/gke-blog/create-sql.png)

在您创建了 CloudSQL 实例之后，我们将为 django 项目创建一个数据库用户和一个数据库，并将`CREATE`、`READ`、`UPDATE`、`DELETE`授予一个我称之为`myproject-user`的用户，并授予我们称之为`myproject-db`的数据库。

我们需要记住的是私有 IP 地址(可以在`CONNECTION`选项卡下找到)。数据库名是`myproject-db`，数据库用户是`myproject-user`，密码是您在创建用户时设置的。我们以后会需要这些东西。

### 将 docker 映像推送到 Google docker 注册表(GKE 集群部署先决条件)

在我们创建一个集群之前，我们想要弄清楚你的项目 ID 是什么，以便使用 Google cloud docker registry `gcr.io`。如果您在 django 应用程序的 docker 文件目录中，您可以这样做:

```
docker build . -t gcr.io/<project-id>/django
docker push gcr.io/<project-id>/django 
```

这将为我们提供一个映像，以便在到达时部署到我们的集群。

### 创建集群

集群是一组将运行您的服务的节点(工作负载的实例，我们将谈到这一点)。

我建议如果这是你第一次创建最小的物理节点类型，并且只创建一个节点。我知道这有点违背了初衷，但是它会在你解决所有问题的时候帮你省钱。另外，单击`Your first cluster`模板也是一个好的开始，但是我们需要单击集群设置底部附近的`Availability, networking, security, and additional features`链接，我们希望启用 VPC 本地设置。

[![VPC Native](img/af5ed3228d5d303373732f78103da434.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6cixZg2k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fergyfresh/fergyfresh.github.io/master/assets/gke-blog/vpc-native.png)

### 部署一个工作负载

在您单击“workload”选项卡下的“deploy”后，您会看到一个在“image”部分有 nginx 的窗口，我们需要使用我们自己的窗口。为此，您需要单击蓝色的选择链接并导航到我们之前创建的图像，这里是我需要您填写您在上一节中设置的环境变量的地方，我说过这些是需要记住的事情。

[![Workload finish](img/37cec04f1eafa764accddb0fb1915d50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OuN7cqPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fergyfresh/fergyfresh.github.io/master/assets/gke-blog/workload-finish.png)

### 向世界公开一项服务。

如果我们进入服务部分，并为我们刚刚部署的工作负载选择服务，我们可以将 django 端口转发到端口 80 或您想要的任何端口！

### 重要关键外卖。

让它工作的最简单的方法是拥有一个支持 VPC 本地的 GKE 集群和一个支持私有 IP 部分的 CloudSQL 实例。

支持文章

*   [GKE 的姜戈](https://cloud.google.com/python/django/kubernetes-engine)
*   [从 Kubernetes 连接到 PostgresQL】](https://cloud.google.com/sql/docs/postgres/connect-kubernetes-engine)

### 如果你搞乱了环境变量

如果您认为您可能错误地设置了一个环境变量，请转到`Configuration`部分，您可以找到一个名为`<service-name>-config`的配置映射。

# 疑问、评论、担忧

请随意点击这些按钮中的一个，以便向我发出信号，告诉我有什么事情搞砸了。我很乐意解决任何对你不起作用的问题。