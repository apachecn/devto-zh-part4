# 关于 Docker 容器你应该知道的 8 件事

> 原文：<https://dev.to/scoutapm/8-things-you-should-know-about-docker-containers-4bdn>

*这篇文章最初出现在[童子军博客](https://scoutapm.com/blog/8-things-you-should-know-about-docker-containers)上。*

这些天 Docker 无处不在！自从这款广受欢迎的开源容器工具在 2013 年首次推出以来，它已经彻底改变了我们部署应用的方式。但是，如果你错过了集装箱化的机会，并对 Docker 到底是什么以及它如何为你带来好处感到困惑，那么我们将这篇文章放在一起，帮助你消除任何困惑。

## 1。Docker 是什么？

Docker 是一个工具，可以让你轻松地在容器中创建、运行和部署你的应用。这些轻量级的虚拟容器可以很容易地部署在服务器上，而不必担心底层系统的细节。这给了开发人员很大的权力和灵活性，并允许我们在不同的环境中部署时避免“依赖地狱”问题。Docker 是一种开源技术，它直接构建在 Linux 容器之上，这使得它比 VM(虚拟机)更快、更轻量级。

## 2。什么是 Docker 容器？

一个**容器**可以被认为是软件的一个标准单元。一个独立的包裹，如果你喜欢的话。运行该软件所需的一切，如代码、库、工具和依赖项，都打包在一起。然后，这个简洁的小软件包可以在不同的环境中被复制和克隆(登台、实时系统、实验等)。)但是不管底层架构是什么，它总是以相同的方式运行。

现在，您可能会认为这听起来非常类似于 VM，您是对的，确实如此！但是容器有一些关键的不同，这使得它们对于软件应用程序的部署特别有用。容器和虚拟机之间的主要区别在于，容器虚拟化了操作系统，而虚拟机虚拟化了 T2 硬件。这允许容器彼此共享元素，比如运行容器的机器的底层操作系统内核。这提供了更高效的性能、更小的文件大小和更快的部署。

那么我们如何创建一个这样的容器呢？首先我们从一个图像开始，我们根据我们的需要定制它，然后我们运行它。这就引出了下一个问题，到底什么是 Docker 图像？

## 3。什么是 Docker 图像？

一个**映像**是一个可共享的功能块，比如一个 web 服务器，或者一个数据库引擎，或者一个 Linux 发行版。你可以把一张图片想象成你的容器的起点，它们就像是蓝图。每个映像都是完整环境的全新安装。那么，容器就是定制和设置后的映像的运行实例。

这些图像可以在 **Docker Hub** 中托管和下载，以供重复使用，或者保存在您自己的私有存储库中。要在容器中运行图像，需要使用 Docker 引擎。这个运行中的容器是我们前面谈到的完整的包。

## 4。什么是 Docker 引擎？

Docker 引擎是使整个系统工作的运行时的名字。当人们提到 Docker 时，他们通常谈论的是 Docker 引擎。容器在这个 Docker 引擎中运行。运行和管理容器最常见的方式是使用 Docker CLI(命令行界面)应用程序。Docker CLI 与 Docker 引擎通信。

## 5。什么是 Docker CLI？

**Docker CLI**是我们与 **Docker 引擎**通信的方式，所以让我们看看如何使用这个 Docker CLI 创建、运行和删除容器。

运行此命令将列出您机器上的所有图像:

```
$ docker images 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用“pull”命令从 Docker Hub 下载图像:

```
$ docker pull image-name 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以从一个图像创建一个容器，并用“运行”命令在容器中运行它:

```
$ docker run image-name 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要查看存在哪些容器(运行或停止)，我们可以使用带有“-a”标志的“ps”来表示所有:

```
$ docker ps -a 
```

Enter fullscreen mode Exit fullscreen mode

如果您还没有任何图像，您可以从 Docker Hub 的一个简单 hello world 示例开始。“运行”命令将使用您指定的映像启动一个新容器。但是，如果该映像不存在，它将尝试在 Docker Hub 中查找:

```
$ docker run hello-world 
```

Enter fullscreen mode Exit fullscreen mode

要在之后进行清理，您可以通过运行“docker ps -a”找出容器的 ID，然后使用“docker rm”命令删除它，或者使用下面的命令删除所有停止的容器:

```
$ docker container prune 
```

Enter fullscreen mode Exit fullscreen mode

## 6。什么是 Dockerfile？

我们已经看到了如何从命令行创建和运行容器，但是在现实中，用一个命令创建和运行一个具有许多依赖项和启动需求的容器会很快变得非常复杂。因此，我们可以使用一个名为 **Dockerfile** 的特殊文件，我们可以将它放在项目的目录中，并在源代码控制中共享。任何拥有这个 Dockerfile 文件的人都可以以完全相同的方式运行同一个容器。

Docker 文件是一个文件，我们可以在其中描述我们的 Docker 容器。在这里，我们可以定义一些东西，比如要启动的图像文件的名称，我们的应用程序源代码所在的位置，以及启动我们的应用程序需要运行的命令。使用这个 Docker 文件，Docker 拥有在容器中创建和运行我们的应用程序所需的所有信息。对于一个简单的容器，这一个文件是我们完全管理我们的容器所需要的。因此，让我们来看看 Python 项目的 docker 文件样本可能是什么样子。

```
# Start from the official Python image
FROM python:3

# Make /code the working directory
WORKDIR /code

# Copy everything in the working directory to the /code directory inside the container
COPY . /code

# Use the Python installer to install packages defined in requirements.txt
RUN pip install -r requirements.txt

# When the container starts, run the file app.py
CMD ["python", "app.py"] 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了 docker 文件，我们就可以在当前目录下创建一个图像，如下所示(用您自己的名字替换 image-name):

```
$ docker build -t image-name . 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以像这样把它作为一个容器来运行:

```
$ docker run image-name 
```

Enter fullscreen mode Exit fullscreen mode

## 7。什么是 Docker Compose？

在现实世界中，我们的应用程序跨越多个进程。例如，也许我们有一个 web 应用程序，它位于数据库引擎之上，并与 REST API 接口。我们怎样才能让这个系统和容器一起工作呢？

这个想法是每个容器应该只做一个任务，所以我们系统的每一个独立部分都应该在它们自己的容器中。这意味着我们需要一个多容器环境。所以现在我们需要管理这些独立的容器如何协同工作并相互通信，这就是 **Docker Compose** 的用武之地。

要使用 Docker Compose，我们需要创建一个 **docker-compose.yml** 文件以及一个 Dockerfile。docker-compose.yml 文件将多个被称为服务的容器联系在一起。在这个示例文件中，有一个“db”服务和一个“web”服务。“db”服务使用官方的 PostgreSQL 映像,“web”服务使用通过 Dockerfile 文件在当前目录中构建的映像。

```
version: '3'

services:
  db:
    image: postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    depends_on:
      - db 
```

Enter fullscreen mode Exit fullscreen mode

Docker Compose 还有一个独立的 CLI 应用程序，它构建在标准 Docker CLI 之上。一旦我们建立了 Dockerfile 和 docker-compose.yml 文件，我们就可以像这样运行所有连接的容器:

```
$ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

## 8。什么是 Docker Hub？

**Docker Hub** 是 Docker 官方公开的容器图片库。在这里你可以找到 Linux 发行版或数据库引擎等的官方图片。您可以在自己的容器中使用它作为起点。例如，如果您的应用程序使用 PostgreSQL 数据库引擎，那么您可以在 Docker 文件中指定来自 Docker Hub 的官方 PostgreSQL 映像来立即使用它。