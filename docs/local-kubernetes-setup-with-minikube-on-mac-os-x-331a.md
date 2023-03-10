# 在 Mac OS X 上使用 minikube 设置本地 Kubernetes

> 原文：<https://dev.to/metaphorical/local-kubernetes-setup-with-minikube-on-mac-os-x-331a>

# Kubernetes，集装箱注册，Helm…

Minikube 是在本地设置 [Kubernetes](https://kubernetes.io/) (从现在开始 k8s)来测试和试验您的部署的理想工具。

在本指南中，我将尝试帮助您在您的本地机器上安装并运行它，提供一些关于在哪里以及如何完成特定工作的提示，并使它具备 helm 功能(我假设当您使用 k8s 时，在某个时候您会想要了解并使用 Helm、etcd、istio 等)。

> **这是您当地的 k8s 环境搭建指南。**

## Minikube 安装

Minikube 与虚拟机一起工作，为此它可以根据您的偏好和操作系统使用各种选项。在这种情况下，我更喜欢 Oracle 的 VirtualBox。

你可以用 brew 安装所有东西:

```
$ brew cask install virtualbox minikube 
```

在这种情况下，您可能会得到某种与 virtualbox 安装相关的不确定的安装错误，特别是在 Mojave 上，可能以后会出现。

无论它说什么，它很可能是 MacOS X 中的一个新的安全特性，这是你的方式。

**进入系统偏好>安全&隐私**，在**通用**屏幕上你会看到一条(或几条)关于某些软件需要批准才能安装的消息。如果有多个软件，您应该仔细检查列表，并允许安装您需要的软件—在本例中是 Oracle 软件。

完成后，您可以重新运行上面的命令，当它完成时，您应该准备好下一步。

## 运行和访问集群

启动它就像
一样简单

```
$ minikube start 
```

为了更好地利用本地机器的资源，我建议当你不再需要它的时候停止它……有了 VirtualBox 在它的中心，它会很快耗尽你笔记本电脑的电池。

稍后再次启动它会让您回到上次停止的地方:

```
$ minikube stop 
```

您也可以使用 Kubernetes dashboard(当 minikube 运行时):

```
$ minikube dashboard 
```

我将假设您已经在本地安装了 kubectl，并且您已经将它用于一些远程集群，因此您得到了多个上下文。在这种情况下，您需要列出上下文并切换到 minikube one(在下面的命令中，假设默认名称为 ofc，“minikube”)

```
$ kubectl config get-contexts
$ kubectl config use-context minikube 
```

现在，您处于在 minikube 上运行的本地 k8s 集群的上下文中，您可以在其中执行所有 k8s 操作。

## 入口控制器

要运行有 ingress 的部署(我假设大多数都会有)，您将需要 ingress 附加组件:

```
minikube addons enable ingress 
```

确保根据您的本地主机设置入口。这基本上意味着**无论您在入口规则中设置为主机的是什么，都需要在/etc/hosts 文件中设置:**

```
[minikube ip] your.host 
```

其中“[minikube ip]”应替换为实际的 minikube ip。在 minikube ip 之后，它还可以与多个空间分隔的本地主机一起工作。
下面是在 bash 中做这件事的捷径:

```
$ echo "$(minikube ip) local.host" | sudo tee -a /etc/hosts 
```

## 集装箱登记处——即码头工人登记处

> 在本地环境中使用真实的容器注册中心是一个粗略的现实，所以我将提供简单、快速和肮脏的选项，使您可以很容易地将本地工作部署到本地 k8s，但是剥夺了您使用正确的容器注册中心的真正重要的经验。

### 本地集装箱登记处

让您的本地 docker 上下文指向 minikube 上下文:

```
$ eval $(minikube docker-env) 
```

> 要恢复:**$ eval $(docker-machine env-u)**

#### 在 minikube 上下文中，启动本地 docker 注册表:

```
$ docker run -d -p 5000:5000 --restart=always --name registry registry:2 
```

所以，现在你有了本地注册中心(只要你的 docker 在 minikube 的上下文中)。

#### 你现在可以做:

```
$ docker build . -t <your_tag>
$ docker tag <your_tag> localhost:5000/<your_tag>:<version> 
```

此时，您可以使用 **localhost:5000/:** 作为部署中的映像，就这样。

## 使用远程容器回购

要在本地使用远程容器 repo，您需要提供认证方法，即通过 k8s secrets。

对于 ECR、GCR 和 Docker registry 的本地机密管理，我推荐使用名为 registry-creds 的 minikube 插件。我认为除了在本地环境中，它在任何地方使用都不够安全。

```
$ minikube addons configure registry-creds
$ minikube addons enable registry-creds 
```

> **关于 ECR 设置的注意事项**:请确保，如果您正在为 AWS ECR 设置它，并且您没有想要使用的角色 arn(您通常不会有并且它是可选的)，您将其设置为“changeme”或 smt 之类的随机值…它需要值，如果您只是按 enter(因为它是可选的), creds pod 的部署将会失败并使您的生活变得悲惨。

在 AWS ECR 的情况下，这将让您从您的回购直接拉设置 url 作为容器图像，并添加名为 awsecr-cred:
的拉秘密

```
imagePullSecrets:
      - name: awsecr-cred 
```

我必须在这里指出，运行这个本地工作对我来说相当混乱，每一次会议都是新的经验和新的黑客让它工作…不是一个快乐的路径。

## 掌舵

Helm 是 k8s 的包管理器，通常用于跨部署的配置管理。随着该工具的高度普及和采用率的提高，我想以关于将 helm 添加到您的本地 k8s env 的说明来结束本指南。

在这一点上很简单，只需启动 minikube 并:

```
$ brew install kubernetes-helm
$ helm init 
```

> 这应该很快就会被弃用，但在当前情况下，helm 使用名为 **Tiller** 的后端，这是在 **helm init** 执行期间安装/部署的。
> 
> 您应该使用:*$ kubectl describe deploy tiller-deploy—namespace = kube-system*来检查 tiller 部署

**有价值的阅读:**【https://docs.helm.sh/using_helm/】T2

#### 现在，在您决定将测试部署放到云中(或某处的“原始”服务器)之前，您已经有了完整的 k8s 本地环境，能够接受您的所有测试部署。

# 快乐缩放