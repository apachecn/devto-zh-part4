# 如何使用远程 Docker 服务器加速您的工作流程

> 原文：<https://dev.to/digitalocean/how-to-use-a-remote-docker-server-to-speed-up-your-workflow-35f0>

### 简介

构建 CPU 密集型映像和二进制文件是一个非常缓慢和耗时的过程，有时会将您的笔记本电脑变成一个空间加热器。在慢速连接上推送 Docker 图像也需要很长时间。幸运的是，这些问题有一个简单的解决方法。Docker 允许你将所有这些任务卸载到一个远程服务器上，这样你的本地机器就不用做那些艰苦的工作了。

这个特性是在 Docker 18.09 中引入的。它支持通过 SSH 远程连接到 Docker 主机。它只需要很少的客户端配置，只需要一个普通的 Docker 服务器，不需要在远程机器上运行任何特殊的配置。在 Docker 18.09 之前，您必须使用 Docker Machine 来创建远程 Docker 服务器，然后配置本地 Docker 环境来使用它。这种新方法消除了额外的复杂性。

在本教程中，您将创建一个 Droplet 来托管远程 Docker 服务器，并在本地机器上配置`docker`命令来使用它。当然，您可以使用任何您喜欢的云提供商，但是一定要阅读整个教程，以了解您将需要什么样的虚拟机。

## 先决条件

要学习本教程，您需要:

*   数字海洋账户。如果您还没有帐户，您可以[创建一个帐户](https://cloud.digitalocean.com/registrations/new)。
*   [Docker](https://www.docker.com/) 安装在你的本地机器或者开发服务器上。如果你使用的是 Ubuntu 18.04，按照[如何在 Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04) 上安装和使用 Docker 的步骤 1 和 2；否则，请遵循[官方文档](https://docs.docker.com/install/)获取关于在其他操作系统上安装的信息。确保将您的非 root 用户添加到`docker`组，如链接教程的步骤 2 所述。

## 步骤 1–创建 Docker 主机

首先，用相当大的处理能力旋转一个液滴。CPU 优化计划非常适合这个目的，但是标准计划也同样适用。如果您要编译资源密集型程序，CPU 优化计划会提供专用的 CPU 内核，从而加快构建速度。否则，标准计划会提供更均衡的 CPU 与 RAM 比率。

Docker 一键图像为我们处理所有的设置。[点击此链接](https://cloud.digitalocean.com/droplets/new?size=c-8-16gib&image=docker-18-04)从控制面板使用 Docker 创建一个 16GB/8vCPU CPU 优化的 Droplet。

或者，您可以使用`doctl`从本地命令行创建 Droplet。要安装它，请遵循 GitHub 上的 [doctl 自述文件中的说明。](https://github.com/digitalocean/doctl/blob/master/README.md)

以下命令基于 Docker 一键图像在 FRA1 区域创建一个新的 16GB/8vCPU CPU 优化的 Droplet:

```
doctl compute droplet create docker-host \
    --image docker-18-04 \
    --region fra1 \
    --size c-8 \
    --wait \
    --ssh-keys $(doctl compute ssh-key list --format ID --no-header | sed 's/$/,/' | tr -d '\n' | sed 's/,$//') 
```

Enter fullscreen mode Exit fullscreen mode

`doctl`命令使用`ssh-keys`值来指定应该将哪些 SSH 密钥应用到您的新 Droplet。我们使用一个子 shell 调用`doctl compute ssh-key-list`来检索与您的数字海洋账户相关的 SSH 密钥，然后使用`sed`和`tr`命令解析结果，将数据格式化为正确的格式。该命令包括您帐户的所有 SSH 密钥，但是您可以用您帐户中任何密钥的指纹替换突出显示的子命令。

创建 Droplet 后，您将会看到它的 IP 地址以及其他详细信息:

```
ID           Name           Public IPv4        Private IPv4    Public IPv6    Memory    VCPUs    Disk    Region    Image                                 Status    Tags    Features    Volumes
148681562    docker-host    your_server_ip                                    16384     8        100     fra1      Ubuntu Docker 5:18.09.6~3 on 18.04    active 
```

Enter fullscreen mode Exit fullscreen mode

你可以在教程[如何使用 doctl，官方数字海洋命令行客户端](https://www.digitalocean.com/community/tutorials/how-to-use-doctl-the-official-digitalocean-command-line-client)中了解更多关于使用`doctl`命令的信息。

创建 Droplet 后，您就可以使用 Docker 服务器了。出于安全考虑，创建一个 Linux 用户来代替根用户。

首先，以 SSH 作为 **root** 用户:
连接到 Droplet

```
ssh root@your_server_ip 
```

Enter fullscreen mode Exit fullscreen mode

连接后，添加一个新用户。这个命令增加了一个名叫**萨米** :

```
adduser sammy 
```

Enter fullscreen mode Exit fullscreen mode

然后将用户添加到 **docker** 组中，授予它在 docker 主机上运行命令的权限。

```
sudo usermod -aG docker sammy 
```

Enter fullscreen mode Exit fullscreen mode

最后，通过键入`exit`退出远程服务器。

现在服务器已经准备好了，让我们配置本地的`docker`命令来使用它。

## 步骤 2–配置 Docker 以使用远程主机

要使用远程主机作为您的 Docker 主机而不是您的本地机器，设置`DOCKER_HOST`环境变量指向远程主机。该变量将指示 Docker CLI 客户端连接到远程服务器。

```
export DOCKER_HOST=ssh://sammy@your_server_ip 
```

Enter fullscreen mode Exit fullscreen mode

现在，您运行的任何 Docker 命令都将在 Droplet 上运行。例如，如果您启动一个 web 服务器容器并公开一个端口，它将在 Droplet 上运行，并且可以通过您在 Droplet 的 IP 地址上公开的端口进行访问。

要验证您是作为 Docker 主机访问 Droplet，请运行`docker info`。

```
docker info 
```

Enter fullscreen mode Exit fullscreen mode

您将会看到您的 Droplet 的主机名列在`Name`栏中，如下所示:

```
...
Name: docker-host
... 
```

Enter fullscreen mode Exit fullscreen mode

需要记住的一点是，当您运行一个`docker build`命令时，构建上下文(所有可从`Dockerfile`访问的文件和文件夹)将被发送到主机，然后构建过程将运行。根据构建上下文的大小和文件的数量，与在本地机器上构建映像相比，这可能需要更长的时间。一种解决方案是为 Docker 映像创建一个新的专用目录，只复制或链接映像中将要使用的文件，这样就不会无意中上传不需要的文件。

## 结论

您已经创建了一个远程 Docker 主机并本地连接到它。下一次你的笔记本电池快没电了，或者你需要建立一个沉重的 Docker 镜像，使用你闪亮的远程 Docker 服务器，而不是你的本地机器。

您可能也有兴趣了解[如何为生产](https://www.digitalocean.com/community/tutorials/how-to-optimize-docker-images-for-production)优化 Docker 图像，或者[如何专门为 Kubernetes](https://www.digitalocean.com/community/tutorials/building-optimized-containers-for-kubernetes) 优化它们。