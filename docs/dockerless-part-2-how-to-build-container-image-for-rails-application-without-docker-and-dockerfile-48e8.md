# Dockerless，第 2 部分:如何在没有 Docker 和 Dockerfile 的情况下为 Rails 应用程序构建容器映像

> 原文：<https://dev.to/mkdev/dockerless-part-2-how-to-build-container-image-for-rails-application-without-docker-and-dockerfile-48e8>

> 声明:在撰写本文时，mkdev 没有在生产环境中运行容器。下面构建的映像仅用于开发、测试和 CI 系统，不会在生产服务器上运行。一旦 mkdev 决定在生产中使用容器，我们的容器映像的内容和设置将会改变，以便实际上适合生产。阅读这篇文章时，请记住这一点。

在上一篇文章中，我们看到了你想要体验无码头生活的所有理由。我们决定尝试两个新工具来取代 Docker: Buildah 和 Podman。在本文中，我们将了解什么是 Buildah，以及如何使用它将 Ruby on Rails 应用程序放入容器中。

### 什么是容器图像？

在我们学习这个工具之前，让我们先通过阅读文章[A sysadmin ' s guide to containers](https://opensource.com/article/18/8/sysadmins-guide-containers)来了解什么是容器映像。从那里我们了解到容器映像是由两部分组成的 TAR 文件:

1.  容器根文件系统。简单地说，这是一个包含所有常规目录的目录，比如/usr、/home 等。
2.  JSON 文件，一个定义如何运行这个根文件系统的配置文件——执行哪些命令，设置哪些环境变量等等。

容器图像的内容在 [OCI 图像规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)中定义，如果你想了解更多关于容器图像的结构，这是你的目的地。这听起来可能很疯狂，但是你不必对容器图像使用 image-spec，你可以[把它用于其他事情。](https://stevelasker.blog/2019/05/11/authoring-oci-registry-artifacts-quick-guide/)

### 什么是 Buildah？

Buildah 是一个容器映像构建工具，可以生成符合 [OCI 标准的](https://github.com/opencontainers/image-spec)映像。它以单个二进制文件的形式分发，并用 Go 编写。Buildah 在大多数现代 Linux 发行版中都可以作为一个包获得，只要遵循[官方安装说明](https://github.com/containers/buildah/blob/master/install.md)就可以了。

Buildah 只能用来操作图像。它的工作是构建容器映像并将它们推送到注册中心。不涉及任何守护进程。Buildah 也不需要 root 权限来构建映像。这使得 Buildah 作为 CI/CD 管道的一部分特别方便——您可以轻松地在容器中运行 [Buildah，而无需授予该容器任何根权限。](https://developers.redhat.com/blog/2019/04/04/build-and-run-buildah-inside-a-podman-container/)

对我个人来说，基于容器的 CI 系统(例如，带有 Docker executor 的 Gitlab CI)所需的 Docker 设置中的整个 Docker，仅仅是为了能够构建新的容器映像，感觉有点矫枉过正。使用 Buildah 就不需要这样了，因为它只关注需要做好的事情和根本不应该做的事情。

> Buildah 非常有用的一个地方是 OpenShift 中的 [BuildConfigurations。从 OpenShift 4.0 开始，BuildConfigs 将依赖于 Buildah 而不是 Docker，从而消除了在 OpenShift 平台中共享任何套接字或拥有特权容器的需要。不用说，它导致了在最流行的容器平台中构建容器映像的更安全和更干净的方式。](https://docs.openshift.com/container-platform/3.11/dev_guide/builds/index.html)

Docker 可以毫无问题地使用 Buildah 构建的图像。它们不是“Buildah 图像”，而仅仅是“容器图像”，它们遵循 OCI 规范，Docker 也理解这一点。那么我们如何用 Buildah 构建一个图像呢？

### 同 Buildahfile

开个玩笑，实际上不涉及 Buildahfile。相反，Buildah 可以直接读取 Docker 文件，尽可能容易地从 Docker 转换到 Buildah。

在 mkdev，我们在我们的[消息平台](https://mkdev.me/en/mentorship)的核心使用 [Mattermost](https://mattermost.com/) 。重要的是，我们能够在本地运行 Mattermost，以便能够轻松地开发主要 web 应用程序和消息传递系统之间的集成。

尽管 Mattermost 已经提供了官方的 Docker 镜像，但由于我们更喜欢配置它的方式，也为了更容易运行 Mattermost 的短暂测试实例，我们不得不构建自己的镜像。我们还想预装某些 Mattermost 插件，这些插件是我们的导师所依赖的。所以我们采用了官方的 docker 文件，对其进行了一点修改，并将其输入到 Buildah:

```
FROM alpine:3.9

# Some ENV variables
ENV PATH="/opt/mattermost/bin:${PATH}"
ENV MM_VERSION=5.8.0
# Set defaults for the config
ENV MMDBCON=localhost:5432 \
    MMDBKEY=XXXXXXXXXXXX \
    MMSMTPUSERNAME=postfix \
    MMSMTPPASSWORD=secrets \
    MMSMTPSALT=XXXXXXXXXXXX \
    MMGITHUBSECRET=secret \
    MMGITHUBHOOK=localhost
# Build argument to set Mattermost edition
ARG PUID=2000
ARG PGID=2000
# Install some needed packages
RUN apk add --no-cache \
 ca-certificates \
 curl \
 jq \
 libc6-compat \
 libffi-dev \
 linux-headers \
 mailcap \
 netcat-openbsd \
 xmlsec-dev \
 && rm -rf /tmp/*
## Get Mattermost
RUN mkdir -p /opt/mattermost/data /opt/mattermost/plugins /opt/mattermost/client/plugins \
    && cd /opt \
    && curl https://releases.mattermost.com/$MM_VERSION/mattermost-team-$MM_VERSION-linux-amd64.tar.gz | tar -xvz \
    && curl -L https://github.com/mattermost/mattermost-plugin-github/releases/download/v0.7.1/github-0.7.1.tar.gz -o /tmp/github.tar.gz \
    && cd /opt/mattermost/plugins \
    && tar -xvf /tmp/github.tar.gz
COPY files/entrypoint.sh /
COPY files/mattermost.json /opt/mattermost/config/config.json
RUN chmod +x /entrypoint.sh \
    && addgroup -g ${PGID} mattermost \
    && adduser -D -u ${PUID} -G mattermost -h /mattermost -D mattermost \
    && chown -R mattermost:mattermost /opt/mattermost /opt/mattermost/plugins /opt/mattermost/client/plugins
USER mattermost
# Configure entrypoint and command
ENTRYPOINT ["/entrypoint.sh"]
WORKDIR /opt/mattermost
CMD ["mattermost"]
# Expose port 8000 of the container
EXPOSE 8065
# Declare volumes for mount point directories
VOLUME ["/opt/mattermost/data", "/opt/mattermost/logs", "/opt/mattermost/config", "/opt/mattermost/plugins", "/opt/mattermost/client/plugins"] 
```

Enter fullscreen mode Exit fullscreen mode

如果它看起来和其他普通的 other 文件一样，那只是因为它实际上就是一个普通的 other 文件。让我们运行 Buildah:

```
buildah bud -t docker.io/mkdevme/mattermost:5.8.0 . 
```

Enter fullscreen mode Exit fullscreen mode

接下来的输出类似于您运行 *docker build 时看到的内容。*命令。生成的图像将被保存在本地，你可以在运行`buildah images`命令时看到它。Buildah 的一个很好的小特性是，您的图像是特定于用户的，这意味着只有构建了这个图像的用户才能看到和使用它。如果您以任何其他系统用户的身份运行`buildah images`，您将看不到任何东西。这与 Docker 不同，在 Docker 中，`docker images`总是为所有用户列出相同的一组图像。

一旦构建了映像，就可以将它推送到注册表中。Buildah 支持多种传输方式来推送您的映像。一些传输示例是`docker-daemon` -如果您仍在本地运行 Docker，并且您希望 Docker 看到此映像，`docker` -如果您希望将映像推送到 Docker API 兼容的远程注册表。还有其他不特定于 Docker 的传输:oci、containers-storage、dir 等。

如果您选择 Docker Hub，那么没有什么可以阻止您使用 Buildah 将图像推送到 Docker Hub。通过使用 Buildah，我们不再考虑 Docker 图像。这更像是如果我们有一个 Git 存储库，我们可以将它推送到 GitHub、GitLab 或 BitBucket。同样，我们可以将我们的集装箱图像推送到选择的注册中心——Docker Hub、Quay、AWS ECR 等。

### 检查图像

Buildah 支持的传输之一是`dir`。当您将您的映像推送到`dir`时，它只是文件系统上的一个目录，Buildah 将在那里存储您的映像的层和配置的 tarballs 以及一个 JSON 清单文件。这只对调试有用，并且非常适合查看图像的内部。

创建一些目录并运行`buildah push IMAGE dir:/$(pwd)`。我不指望你真的建立一个 Mattermost 图像，只是使用任何其他图像。如果你没有任何图像，也不想构建任何图像，那么只需`buildah pull`Docker Hub 中的任何图像。

一旦完成，你会看到文件名类似于`96c6e3522e18ff696e9c40984a8467ee15c8cf80c2d32ffc184e79cdfd4070f6`的文件，它实际上是一个 tarball。您可以将该文件解压缩到您选择的目标位置，并查看该图像层中的所有文件。您还会看到一个 image manifest.json 文件，在 Mattermost 的情况下，它看起来像这样:

```
{  "schemaVersion":  2,  "config":  {  "mediaType":  "application/vnd.oci.image.config.v1+json",  "digest":  "sha256:57ea4e4c7399849779aa80c7f2dd3ce4693a139fff2bd3078f87116948d1991b",  "size":  1262  },  "layers":  [  {  "mediaType":  "application/vnd.oci.image.layer.v1.tar",  "digest":  "sha256:6bb94ea9af200b01ff2f9dc8ae76e36740961e9a65b6b23f7d918c21129b8775",  "size":  2832039  },  {  "mediaType":  "application/vnd.oci.image.layer.v1.tar",  "digest":  "sha256:96c6e3522e18ff696e9c40984a8467ee15c8cf80c2d32ffc184e79cdfd4070f6",  "size":  162162411  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

图像清单由 OCI 规范描述[。如果你仔细看看上面的例子，它定义了两个层(vnd.oci.image.layer.v1.tar)和一个配置文件(vnd.oci.image.config.v1+json)。我们可以看到配置有一个摘要`57ea4e4c7399849779aa80c7f2dd3ce4693a139fff2bd3078f87116948d1991b`。我们也有这个文件，虽然它看起来像层文件，但它实际上是图像的配置文件。](https://github.com/opencontainers/image-spec/blob/master/manifest.md)

这可能有点令人困惑，但请记住，这种结构是为其他软件存储和处理而创建的，而不是供人阅读的。如果您需要快速判断映像中的哪个文件存储了配置，请首先查看 manifest.json】

```
{  "created":  "2019-05-12T16:13:28.951120907Z",  "architecture":  "amd64",  "os":  "linux",  "config":  {  "User":  "mattermost",  "ExposedPorts":  {  "8065/tcp":  {}  },  "Env":  [  "PATH=/opt/mattermost/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",  "MM_VERSION=5.8.0",  "MMDBCON=localhost:5432",  "MMDBKEY=XXXXXXXXXX",  "MMSMTPUSERNAME=postfix",  "MMSMTPPASSWORD=secrets",  "MMSMTPSALT=XXXXXXXXXX",  "MMGITHUBSECRET=secret",  "MMGITHUBHOOK=localhost"  ],  "Entrypoint":  [  "/entrypoint.sh"  ],  "Cmd":  [  "mattermost"  ],  "Volumes":  {  "/opt/mattermost/client/plugins":  {},  "/opt/mattermost/config":  {},  "/opt/mattermost/data":  {},  "/opt/mattermost/logs":  {},  "/opt/mattermost/plugins":  {}  },  "WorkingDir":  "/opt/mattermost"  },  "rootfs":  {  "type":  "layers",  "diff_ids":  [  "sha256:f1b5933fe4b5f49bbe8258745cf396afe07e625bdab3168e364daf7c956b6b81",  "sha256:462e838baed1292fb825d078667b126433674cdc18c1ba9232e2fb8361fc8ac2"  ]  },  "history":  [  {  "created":  "2019-05-11T00:07:03.358250803Z",  "created_by":  "/bin/sh -c #(nop) ADD file:a86aea1f3a7d68f6ae03397b99ea77f2e9ee901c5c59e59f76f93adbb4035913 in / "  },  {  "created":  "2019-05-11T00:07:03.510395965Z",  "created_by":  "/bin/sh -c #(nop) CMD [\"/bin/sh\"]",  "empty_layer":  true  },  {  "created":  "2019-05-12T16:13:28.951120907Z"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

因此，只有一堆 tarballs 和 json 文件——这就是整个容器映像！

### 你说无 docker 却还依赖 Dockerfile！

Buildah 的创建者有意决定不引入新的 DSL 来定义容器图像。Buildah 提供了两种定义图像的方法:Dockerfile 或一系列的`buildah`命令。我们将很快学习第二种方法，但是我必须警告你，我不认为 Dockerfiles 会很快消失。除了名字本身，可能没有任何东西伴随着它们。想象一下，投资开发 Dockerless，却发现自己仍在编写 Dockerfiles！

我希望它们被称为容器文件或图像文件。这对社区来说就不会那么尴尬了。但是现在，惯例是将这个文件命名为 Dockerfile，我们只需处理它。

### 直接用 Buildah 构建图像

用 Buildah 构建映像的第二种方法是使用`buildah`命令。Buildah 构建映像的方式是从一个基础映像创建一个新的容器，然后在这个容器中运行命令。在所有命令运行之后，您可以提交这个容器成为一个图像。让我们以这种方式构建一个图像，然后讨论这是否以及何时比编写 docker 文件更好。

我们首先需要从现有的图像开始一个新的容器:

```
buildah from centos:7 
```

Enter fullscreen mode Exit fullscreen mode

如果图像还不存在，它将被从注册表中取出，就像使用 Docker 时一样。`buildah from`命令将返回启动的容器的名称，通常是“IMAGE_NAME-working-container”，在我们的例子中是`centos-working-container`。我们需要记住在以后的所有命令中使用这个名称。

我们可以用`buildah run`命令:
在这个容器中运行命令

```
buildah run centos-working-container -- yum install unzip -y 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用`buildah config`命令为未来的图像配置各种 OCI 兼容的选项，例如环境变量:

```
buildah config -e ENVIRONMENT=test centos-working-container 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以在构建服务器中挂载完整的容器文件系统，并使用安装在主机上的工具直接从主机操作它。当我们不想仅仅为了做一些构建时操作而在映像中安装某些工具时，这是很有用的。请记住，在这种情况下，您需要确保所有这些工具都安装在想要构建您的映像的任何人的机器上(这可能会破坏您的构建脚本的可移植性)。

```
buildah mount centos-working-container 
```

Enter fullscreen mode Exit fullscreen mode

作为回报，Buildah 会给你一个挂载文件系统的位置，例如`/home/fodoj/.local/share/containers/storage/overlay/DIGEST/merged`。为了测试，我们可以在那里创建一个文件:`touch hello-from-host /home/fodoj/.local/share/containers/storage/overlay/DIGEST/merged/home/`。

一旦我们对图像感到满意，我们就可以提交它:

```
buildah commit centos-working-container my-first-buildah-image 
```

Enter fullscreen mode Exit fullscreen mode

并移除工作容器:

```
buildah rm centos-working-container 
```

Enter fullscreen mode Exit fullscreen mode

请注意，尽管 Buildah 确实运行容器，但它没有提供一种除了构建图像之外对任何事情都有用的方式。Buildah 不是容器引擎的替代品，它只提供一些原语来调试构建映像的过程！

Buildah 构建的图像对 Podman 是可见的，这将是下一篇文章的主题。现在，如果您想验证文件`hello-from-host`确实存在，运行这个:

```
image=$(buildah from my-first-buildah-image)
ls $(buildah mount $image)/home
$> hello-from-host 
```

Enter fullscreen mode Exit fullscreen mode

这将创建另一个工作容器。挂载它并显示/home 目录的内容。如果你想用 Buildah 而不用 Dockerfile 来构建图像，我们这样做实际上是可行的。您应该编写一个 shell 脚本来调用所有命令，提交图像并删除工作容器，而不是 Dockerfile。这就是 mkdev 的“Buildahfile”(这实际上只是一个 shell 脚本)的样子:

```
#!/bin/bash
set -x
mkdev=$(buildah from centos:7)
buildah run "$mkdev" -- curl -L http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm -o epel-release-latest-7.noarch.rpm
buildah run "$mkdev" -- curl -L https://downloads.wkhtmltopdf.org/0.12/0.12.5/wkhtmltox-0.12.5-1.centos7.x86_64.rpm -o wkhtmltopdf.rpm
buildah run "$mkdev" -- curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
buildah run "$mkdev" -- rpm -ivh epel-release-latest-7.noarch.rpm
buildah run "$mkdev" -- yum install centos-release-scl -y
buildah run "$mkdev" -- yum install unzip postgresql-libs postgresql-devel ImageMagick \
                       autoconf bison flex gcc gcc-c++ gettext kernel-devel make m4 ncurses-devel patch \
                       rh-ruby25 rh-ruby25-ruby-devel rh-ruby25-rubygem-bundler rh-ruby25-rubygem-rake \
                       rh-postgresql96-postgresql openssl-devel libyaml-devel libffi-devel readline-devel zlib-devel \
                       gdbm-devel ncurses-devel gcc72-c++ \
                       python-devel git cmake python2-pip chromium chromedriver which -y
buildah run "$mkdev" -- pip install ansible boto3 botocore
buildah run "$mkdev" -- yum install wkhtmltopdf.rpm -y
buildah run "$mkdev" -- ln -s /usr/local/bin/wkhtmltopdf /bin/wkhtmltopdf
buildah run "$mkdev" -- unzip awscli-bundle.zip
buildah run "$mkdev" -- ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
buildah run "$mkdev" -- yum clean all && rm -rf /var/cache/yum
git archive -o app.tar.gz --format=tar.gz HEAD
buildah add "$mkdev" app.tar.gz /app/
buildah add "$mkdev" infra/app/build/entrypoint.sh /entrypoint.sh
buildah config --workingdir /app "$mkdev"
buildah run "$mkdev" -- scl enable rh-ruby25 "bundle install"
rm app.tar.gz
buildah config --port 3000 "$mkdev"
buildah config --entrypoint '[ "/entrypoint.sh" ]' "$mkdev"
buildah run "$mkdev" -- chmod +x /entrypoint.sh
buildah config --cmd "bundle exec rails s -b '0.0.0.0' -P /tmp/mkdev.pid" "$mkdev"
buildah config --env LC_ALL="en_US.UTF-8" "$mkdev"
buildah run "$mkdev" -- rm -rf /app/
buildah commit "$mkdev" "docker.io/mkdevme/app:dev"
buildah rm "$mkdev" 
```

Enter fullscreen mode Exit fullscreen mode

如果您曾经制作过一个好的容器图像，那么这个脚本对您来说可能看起来极其愚蠢。让我解释一下那里正在发生的一些事情:

1.  我们使用 Centos 7 作为基本映像，因为在生产环境中，我们在 Centos 7 上运行。即使我们还没有在生产中运行容器，让开发环境尽可能接近生产环境也是有意义的。
2.  我们确实安装了数量惊人的软件包，包括 AWS CLI、Chromium、软件集合等等。我们这样做是因为我们在开发环境和 CI 系统中使用了生成的映像。这两个位置都需要额外的工具来运行集成测试(Chromium)或者执行一些打包和部署任务(AWS CLI 和 Ansible)。我们的生产环境中使用软件集合，在所有其他 env 中使用相同的 Ruby 版本也很重要。
3.  我们在最后删除了应用程序本身的代码。对于这个用例，我们真的不需要代码在图像中。在开发环境和 CI 中，我们都需要最新版本的代码，而不是嵌入到映像中的东西。

我们将这个脚本存储在应用程序 repo 中，就像我们将 docker 文件保存在那里一样。一旦我们决定要在生产中的容器中运行 mkdev，我们就可以修改这个脚本来根据环境做不同的事情。

只有当您的构建服务器能够运行 shell 脚本时，您才可以使用这种方法。这不是问题，因为 Windows 有 WSL。你的主机系统不一定是基于 Linux 的，只要它能够在内部运行某种类型的 Linux！在没有额外的 Linux VM 的情况下，它有一天会对 MacOS 用户起作用吗？谁知道呢，让我们希望 Buildah 开发者正在努力。

### Buildah 内部是如何工作的？

波德曼和 Buildah 的内部工作非常相似。它们都利用了 Linux 内核特性，特别是用户名称空间和网络名称空间，使得在没有任何 root 权限的情况下运行容器成为可能。我不会在本文中谈论它，但是如果你等不及了，那么就从阅读下面的资源开始吧:

*   [无根 Buildah 如何工作:在无特权环境中构建容器](https://opensource.com/article/19/3/tips-tricks-rootless-buildah)
*   Podman:一种更安全的运行容器的方式
*   [波德曼和用户名称空间:天作之合](https://opensource.com/article/18/12/podman-and-user-namespaces)。

### 接下来是什么

我希望你今天学到了很多关于容器图像的知识。Buildah 不仅是本地开发的好工具，也是构建容器映像的任何自动化的好工具。这不是唯一可用的，谷歌的 Kaniko 是另一个例子，尽管 Kaniko 更关注 Kubernetes 环境。

现在我们已经有了一个映像，是时候运行它了。在下一篇文章中，我将向您展示如何使用 Podman 来完全自动化 Ruby on Rails 应用程序的本地开发环境。我们将学习如何使用 Podman 的 Kube YAML 特性来描述符合 Kubernetes 的 YAML 定义中的所有服务，如何在容器中运行 Rails 应用程序，以及如何在该容器中运行 Rails 应用程序的测试。当我们开始为集成测试创建短暂的 Mattermost 实例时，容器，尤其是 Podman 将变得非常方便。

欢迎在下面的评论中提出任何问题，我将确保直接回复他们或扩展这篇文章！

* * *

*这是[一篇由](https://mkdev.me/en/posts/dockerless-part-2-how-to-build-container-image-for-rails-application-without-docker-and-dockerfile)[基里尔·希林金](https://mkdev.me/en/mentors/fodoj)撰写的mkdev 文章。你可以聘请我们的 DevOps 导师来亲自学习集装箱化。*