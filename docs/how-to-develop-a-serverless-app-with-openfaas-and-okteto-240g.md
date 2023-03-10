# 如何用 openfans 和 okteto 开发无服务器 app

> 原文：<https://dev.to/okteto/how-to-develop-a-serverless-app-with-openfaas-and-okteto-240g>

### 如何用 OpenFaaS 和 Okteto 开发无服务器 App

<figure>[![](img/9dfe3da86a03363779acdcbfd5375ed9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qVFQNrhm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQ5JCUZlucY-FikYWj6o_Zw.jpeg)

<figcaption>Okteto+open FAAS</figcaption>

</figure>

OpenFaaS (Functions as a Service)是一个用 Docker 和 Kubernetes 构建无服务器功能的框架。

OpenFaaS 通过帮助您将应用程序逻辑打包到对 web 事件做出反应的离散包中，简化了您的应用程序。OpenFaaS 不需要部署数十个 pod 来保持您的应用程序大规模运行，而是根据 web 事件和指标自动独立地扩展您的功能。

在这篇博文中，我们将向您展示如何部署您自己的 OpenFaaS 实例，启动您的第一个功能，以及如何开发它。然后，我们将向您展示如何使用 Okteto CLI 来进一步加速您的无服务器开发。

### 在 Okteto Cloud 部署 OpenFaaS

如果您已经安装了 OpenFaaS，请随意跳到下一步。

在这篇文章中，我们将把 OpenFaaS 部署到 [Okteto Cloud](https://cloud.okteto.com) 中。Okteto Cloud 是一个[自助式多租户 Kubernetes 集群](https://okteto.com/docs/cloud)，针对团队协作和云原生开发进行了优化。

登录 [Okteto Cloud](https://cloud.okteto.com) ，点击部署按钮。从舵图中将部署方式切换到**部署，选择 OpenFaaS，选择密码，点击部署。您的 OpenFaaS 实例将在几秒钟内启动并运行。**

[![Deploy OpenFaaS in okteto](img/ed8d30cd703a4a8a2df565c7a52367c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EdryJPq9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/hl48fjdbmtd752bdc3in.png)

OpenFaaS 包括一个 web 网关作为部署的一部分，可以用来查看您的功能、创建新的功能以及调用它们。Okteto Cloud 自动为您的 OpenFaaS 网关创建了一个入口。在浏览器中打开 [Okteto Cloud](https://cloud.okteto.com) ，点击网关 URL 即可访问。

首次打开网关时，系统会提示您输入凭据。用户是 admin，密码将是您在部署对话框中提供的`Password`字段的内容(默认为`Password123!`)。

[![The Gateway UI via Okteto](img/c52a2a8330561e69d5c70fc18c8f8742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VNo6CGTa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/3oc3vxea7c05zuo4zvr5.png)

### 安装 OpenFaaS CLI

我们需要本地可用的 OpenFaaS CLI 来部署功能。如果您使用的是 Mac 或 Linux，运行下面的命令来安装它:

```
$ curl -sL cli.openfaas.com | sudo sh 
```

在 Windows 上，从[发布页面](https://github.com/openfaas/faas-cli/releases)下载最新的 faas-cli.exe，并将其放在您的 PATH 中。

通过打开终端并运行:
来验证安装是否正确

```
$ faas-cli help
$ faas-cli version 
```

要从 CLI 访问 OpenFaaS 网关，您需要导出`$OPENFAAS_URL`和`$OPENFAAS_PASSWORD`环境变量(您可以从 [Okteto Cloud 的](https://cloud.okteto.com)获得您网关的 URL)并使用 faas-cli 登录命令
登录

```
export OPENFAAS_URL=$OPENFAAS_GATEWAY_URL
export OPENFAAS_PASSWORD="Password123!"
$ echo $OPENFAAS_PASSWORD | faas-cli login -u admin --password-stdin

Calling the OpenFaaS server to validate the credentials...
credentials saved for admin https://gateway-rberrelleza.cloud.okteto.net 
```

### 部署您的第一个功能

现在我们有了 OpenFaaS 的实例，是时候部署我们的第一个函数了。

OpenFaaS 支持几乎所有的编程语言，但是因为我是 golang 的超级粉丝，所以我们将在这篇文章中使用它。使用`faas-cli` new 命令创建所有必要的文件。

```
$ faas-cli new -lang go gohash
...
  ___                   _____           ____
 / _ \ _ __   ___ _ __ |  ___|_ _  __ _/ ___|
| | | | '_ \ / _ \ '_ \| |_ / _` |/ _` \___ \
| |_| | |_) |  __/ | | |  _| (_| | (_| |___) |
 \___/| .__/ \___|_| |_|_|  \__,_|\__,_|____/
      |_|Function created in folder: gohash
Stack file written: gohash.yml
... 
```

我们现在有一个名为 gohash 的文件夹和一个名为`handler.go`的文件，这是我们函数的主要代码。我们还有一个名为`gohash.yml`的文件，其中包含函数元数据，还有一个名为 template 的文件夹，其中包含不同的可用函数模板。

用你最喜欢的文本编辑器打开`gohash.yml`，把你的 Docker Hub 账号放到`image:`部分。亦即`image: okteto/gohash:latest`。

然后，运行 faas-cli up 命令来构建、推送和部署您的函数。

> faas-cli up 命令使用您的本地 Docker 客户端来构建和推送映像。它要求您登录 Docker Hub 或类似的注册表。

```
$ faas-cli up -f gohash.yml
[0] > Building gohash.
...
[0] < Building gohash done.
[0] worker done.

[0] > Pushing gohash [ramiro/gohash:latest].
...
[0] < Pushing gohash [ramiro/gohash:latest] done.
[0] worker done.

Deploying: gohash.

Deployed. 202 Accepted.
URL: https://gateway-openfaas-rberrelleza.cloud.okteto.net/function/gohash 
```

一旦部署了您的功能，我们将使用网关的 UI 来调用它。在浏览器中打开网关，单击左边的 gohashon，键入 testas 作为请求体，然后单击 _Invoke _button。

<figure>[![](img/6d89f171067d10a409510b5e61ffd7be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1k7cK29A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AX4w2Z2YOqM6hZDUqksVuTA.png) 

<figcaption>您的第一个 OpenFaaS 函数</figcaption>

</figure>

### 开发你的功能

一旦我们的功能启动并运行，让我们添加一个简单的功能。我们不只是回显输入，而是计算字符串的校验和并返回它。

用您最喜欢的编辑器打开`gohash/handler.go`，并更新它: