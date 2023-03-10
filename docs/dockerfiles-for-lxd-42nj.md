# LXD 的码头文件

> 原文：<https://dev.to/makkus/dockerfiles-for-lxd-42nj>

## tldr；

[*雀斑*](https://freckles.io) 可以作为一个通用的、可组合的‘docker file’替代 LXD。为了进行测试，在安装了*雀斑*和 LXD 的系统上，将以下内容放入文件`'example-lxd.frecklet`:

```
- nginx-service
- nginx-vhost-from-folder:
    document_root: /var/www/html
    default_server: true
    use_https: false
- file-with-content:
    owner: www-data
    path: /var/www/html/index.html
    content: |
      <h1><i>freckles</i> says "Hello World", from {{:: from ::}}!</h1> 
```

这将作为我们的“Dockerfile ”,描述如何安装和配置 nginx 来服务静态网页。要从它创建一个 LXD 图像，执行:

```
$  frecklecute lxd-image-from-frecklet-file \
     --install-packer \
     --source-image images:debian/10 \
     --image-name freckles-image \
     --frecklet-path $(pwd)/example-lxd.frecklet \
     --frecklet-vars '{"from": "my first image"}' 
```

一旦完成，从新的“雀斑图像”图像创建一个容器:

```
$  lxc launch freckles-image freckles-test 
```

要测试容器中运行的 Nginx 服务是否工作，发出:

```
$  curl http://<container_ip>
<h1><i>freckles</i> says "Hello World", from my first image!</h1> 
```

## 长篇序言

我认为 Dockers 文件在 Dockers 早期的成功和采用中发挥了相当大的作用。它们易于阅读/理解，足够灵活，除了基本任务之外还非常有用，并且是可组合的(如果你稍微眯着眼睛，把“从另一个 Docker 图像继承”看做组合)。

当然，码头工人的成功还有其他因素，其中包括在正确的时间和正确的地点(也就是运气)，但码头工人的档案当然很重要。Docker 容器通常被认为是“应用程序容器”，是微服务类型架构的一部分。在实践中，这是(可能从来不是)100%正确的，有很多人试图强迫 Docker 做其他事情，例如为 Docker 开发“init-systems ”,试图在一个容器中运行 webapps，包括数据库和 Memcache 以及其他任何东西。不管你怎么想，这仍然是 Dockers 成功的证明，以及 Dockerfiles 的灵活性，因为他们实际上允许所有这些被建立。

现在，如果我们只是接受这个前提，那么在一些情况下，在同一台主机上拥有多个服务是有利的，而没有连接所有服务的虚拟网络的开销(我们当然可以争论这什么时候有意义，什么时候没有意义)，我们迟早会遇到 LXD(我会忽略更低级的 LXC，但我承认有时它会更适合)。

[LXD](https://linuxcontainers.org/lxd/introduction/) 是一个所谓的“系统容器管理器”(相对于“应用程序容器”)，这意味着它“包含”整个操作系统，包括一个“正常的”init 系统。除了一些与权限相关的事情之外，LXD 容器的行为就像物理或虚拟机一样，但是没有太多的开销(例如，像“真实的”虚拟机)。

不过 LXD 没有 Dockerfile(暂时忽略“cloud-init”——将*雀斑*与“cloud-init——一起使用是另一篇博文的主题)。其中一个原因是，这并不是*真正的*必要的。由于 LXD 容器的行为类似于普通的物理或虚拟机，我们可以使用相同的工具来安装应用程序，并且通常使容器进入我们想要的状态。对于 sysadmin/devops 专业人员进行状态创建的情况，这很好，他们知道做什么，以及如何以自动化的方式来做。使用像 Ansible，Puppet 这样的工具。或者可能是 Packer、Terraform 等。

在这种情况下,“LXD 的 Dockerfile”的一个优势是为 LXD 那些不需要经常处理这些事情的人带来了简单的可复制性、可重用性和一般的“有序性”。以同样的方式，Dockerfiles 把这些东西和 Docker 一起带来了。你所需要的只是一个文本文件，你将能够复制和定制一个 Docker 图像。我认为我们不能高估一项技术的作用，这项技术允许知道他们在做什么的人(那些创建 Dockerfiles 的人)与那些不知道如何做特定事情(至少没有在 Stackoverflow 上花费几个小时)或不能被打扰的人分享他们正在做的事情(Dockerfiles)，他们现在可以重用特定的事情。

顺便说一下，现在只需要创建一次的东西，然后可以由一群人(理想情况下他们知道自己在做什么)不断改进。这就像在任何编程语言中使用专用库一样，它允许您作为应用程序开发人员专注于您的核心问题，而不必过多地关注那些您原本要担心的无关紧要的细节。从各方面考虑，这真的不是什么大问题。但是这种现象非常普遍，以至于我们在日常(工作)生活中经常看不到也没有意识到，这意味着我们没有利用现有的解决方案来解决这种问题。

顺便说一句，这也正是[雀斑](https://freckles.io)试图解决的问题，只是比 does 文件用更普通的方式。允许可重用的方法将计算环境带入一个特定的状态，独立于技术和堆栈。

最后，这让我想到了这篇文章的主题:使用 [*frecklets*](https://freckles.io/doc/frecklets) 作为某种可重用的“Dockerfile-replacements”。在 LXD 的背景下。当然，因为它们是可重用的，你也可以使用你在 LXD 使用的相同的 *frecklet* 用于 Docker(有一些注意事项)，或者，提供一个远程服务器(我将在这篇文章的最后展示)。

## 用*雀斑*用 LXD

我们可以在两个不同的地方选择将*雀斑*与 LXD 结合使用:

*   描述图像的内容/状态
*   建立真实的 LXD 形象

此外，我们可以使用*雀斑*来安装和配置 LXD:

### 可选:使用*雀斑*安装 LXD

**注意**:以下不为连接到新创建的网桥‘lxbr 0’的容器设置互联网连接。这必须手动完成(目前)，例如通过执行`iptables -t nat -A POSTROUTING -s <bridge_subnet>.0/24 -o eth0 -j MASQUERADE`。这将在未来某个时候自动完成，希望如此。

为此，我们可以使用 [`lxd-service`](https://freckles.io/frecklets/default/virtualization/lxd-service) 雀斑:

```
$  frecklecute lxd-service --user markus 
```

在幕后，它使用 [juju4.lxd](https://github.com/juju4/ansible-lxd) Ansible 角色在基于 Ubuntu 或 RedHat 的 Linux 系统上安装 lxd。其他发行版可能可以工作，但是没有经过测试。另外，请注意`--user markus`命令行参数。这可让您指定一个或多个有权使用该主机上的 LXD 服务的用户。运行之后，您必须注销当前会话并重新登录，或者执行`newgrp lxd`来利用这个权限。

使用这个 *frecklet* 可以调整一些与您的 LXD 设置相关的参数(例如网络地址或存储设置)，但这超出了本文的范围。

### 描述 LXD 图像内容

我们将编写一个相对简单的 [*frecklet*](https://freckles.io/doc/frecklets) ，与我们为不同目标类型(例如远程服务器)编写的任何东西没有任何不同。关于如何工作的更多细节，以及你如何写你自己的，请查看 [*雀斑*入门指南](https://freckles.io/doc/getting_started)和[其他雀斑相关的文档](https://freckles.io/doc/frecklets)。

对于我们的例子，让我们安装 Nginx 服务器，并将其配置为托管单个静态 html 站点(我们也动态创建)。

由于这是一件相对“正常”的事情，已经有预制的*雀斑*可以帮助我们实现所需的步骤，它们是:

*   安装 Nginx 包并启用/启动服务( [`nginx-service`](https://freckles.io/frecklets/default/service/nginx-service) )
*   为我们的 vhost ( [`nginx-vhost-from-folder`](https://freckles.io/frecklets/default/service/nginx-vhost-from-folder) )创建一个配置
*   创建我们的 html 文件( [`file-with-content`](https://freckles.io/frecklets/default/filesystem/file-with-content) )

下面是它看起来像是一只*雀斑* :

```
- nginx-service
- nginx-vhost-from-folder:
    document_root: /var/www/html
    default_server: true
    use_https: false
- file-with-content:
    owner: www-data
    path: /var/www/html/index.html
    content: |
      <h1><i>freckles</i> says "Hello World", from {{:: from ::}}!</h1> 
```

注意我们的 *frecklet* 代码中的`{{:: from ::}}`模板字符串:这让我们可以为每个 LXD 图片定制生成的 html(如果我们选择这样做的话)。

为了测试，让我们启动一个 LXC 集装箱，并为它配备我们的新 *frecklet* 。我们可以用一个简单的`lxc launch ...`命令来做到这一点，但是当我们这样做的时候，让我们也用*雀斑*来做到这一点。在交互式会话中，这没有多大意义，因为它也只是一个命令，并且有一点开销。但是，如果启动 LXD 容器是供应管道的一部分，这是一个展示如何做的好机会。所以，这里有:

```
$  frecklecute lxd-container-running --image-name 'debian/10' \
     --image-server https://images.linuxcontainers.org \
     --name 'freckles-test' \
     --register-addresses freckles_container_ip
 ╭╼ starting run
│  ├╼ running frecklet: lxd-container-running (on: localhost)
│  │  ├╼ starting Ansible run
│  │  │  ├╼ create/launch container 'freckles-test'
│  │  │  │  ╰╼ ok
│  │  │  ╰╼ ok
│  │  ╰╼ ok
│  ╰╼ ok
╰╼ ok

Result:

    freckles_container_ip:
      eth0:
      - 10.10.10.42 
```

在这个例子中，我们提供了可选的`--register-addresses`命令行参数。这促使*雀斑*将我们新创建的容器的(网络)细节注册到一个变量中，这使得我们以后更容易连接到它。或者，您可以通过简单的`lxc list`获得 ip 地址。

现在我们的容器正在运行，我们可以从上面使用我们的 *frecklet* 来供应它。我们将内容字符串保存为`example-lxd.frecklet`，然后发出:

```
$  frecklecute --target lxd::freckles-test example-lxd.frecklet --from 'a test run' 
```

最关键的是`--target lxd::<container_name>`部分。它告诉*雀斑*不要通过 *ssh* 连接，而是直接通过‘lxd’连接。

这将需要一两分钟的时间，因为除了安装 Nginx 之外，还需要进行一些引导。一旦该过程完成，您应该能够将您的浏览器指向在此之前运行中显示的 IP 地址，并看到消息“雀斑说“Hello World”，来自一个测试运行！”

太好了，现在我们已经拥有了制作 LXD 集装箱形象所需的一切...

### 打造 LXD 形象

虽然可以使用*雀斑*从头开始创建 LXD 图像，例如通过将[本教程](https://tutorials.ubuntu.com/tutorial/create-custom-lxd-images#0)中的步骤捕捉到*雀斑*中，但我们在这里不会这样做。我们将会作弊一点，使用 [Packer](https://www.packer.io) 代替，因为它提供了相同的功能(和更多)，更少的麻烦。

因为我们已经有了我们的`example-lxd.frecklet`文件，我们需要做的就是在下面的命令中提供它的完整路径:

```
$  frecklecute container-image-from-frecklet-file \
     --install-packer \
     --image-type lxd \
     --source-image images:debian/10 \
     --image-name freckles-image \
     --frecklet-path $(pwd)/example-lxd.frecklet \
     --frecklet-vars '{"from": "my first image"}' 
```

让我们详细看看这个命令:

*   [`container-image-from-frecklet-file`](https://freckles.io/frecklets/default/virtualization/container-image-from-frecklet-file) :我们要执行的[frecklet](https://freckles.io/frecklets/default/virtualization/container-image-from-frecklet-file)的名字(我们也可以使用包装器 *frecklet* [`lxd-image-from-frecklet-file`](https://freckles.io/frecklets/default/virtualization/lxd-image-from-frecklet-file)
*   `--install-packer`:我们确保[打包器](https://www.packer.io)可执行文件在我们的系统上可用(使用罩下的 [`packer-installed`](https://freckles.io/frecklets/default/devops/packer-installed) *frecklet*
*   `--image-type lxd`:我们想要一个 LXD 映像(我们也可以说:`docker`，但是因为在我们的 frecklet 中有一些 systemd/init 的东西，所以在这种情况下不适用)
*   `--source-image images:debian/10`:要使用的源图像(注意前缀‘images:’，这是使用正确的 LXD 图像索引所必需的
*   我们新形象的名字
*   `--frecklet-path $(pwd)/example-lxd.frecklet`(绝对！)到包含我们的供应指令的 *frecklet* 的路径
*   `--frecklet-vars '{"..."}'`:我们的供应 *frecklet* 需要的额外变量(如 JASON 字符串)。使用这种方法的另一种方法是对 *frecklet* 文件中的所有内容进行硬编码

因此，一旦这个命令完成，您应该能够通过:
看到新的图像

```
$  lxc image list
+----------------+--------------+--------+-----------------------------------------------+--------+-----------+-------------------------------+
|     ALIAS      | FINGERPRINT  | PUBLIC |                  DESCRIPTION                  |  ARCH  |   SIZE    |          UPLOAD DATE          |
+----------------+--------------+--------+-----------------------------------------------+--------+-----------+-------------------------------+
| freckles-image | f9600c979eba | no     | built with freckles.                          | x86_64 | 194.72MB  | Aug 10, 2019 at 10:37am (UTC) | ...
... 
```

我们可以这样使用它:

```
$  frecklecute lxd-container-running --image-name 'freckles-image' \
      --name 'freckles-example' --register-addresses freckles_container_ip
 ╭╼ starting run
│  ├╼ running frecklet: lxd-container-running (on: localhost)
│  │  ├╼ starting Ansible run
│  │  │  ├╼ create/launch container 'freckles-example'
│  │  │  │  ╰╼ ok
│  │  │  ╰╼ ok
│  │  ╰╼ ok
│  ╰╼ ok
╰╼ ok

Result:

    freckles_container_ip:
      eth0:
      - 10.10.10.100 
```

现在把你的浏览器指向容器 ip(在我的例子中是 10.10.10.100 ),你会看到一条信息，像是:“雀斑女说‘你好，世界’，来自我的第一张图片！”。

### 重用我们的*雀斑*

我写*雀斑*的主要原因是为了有一个通用的、灵活的和可重用的方式来描述计算环境的期望状态。我们已经使用我们的 *frecklet* 提供了一个运行中的(空的)LXD 容器，并从中创建了一个 LXD 容器映像。如果我们的 *frecklet* 不包含特定于初始化系统的指令(设置和重启 systemd 服务单元)，我们也可以用它来构建 Docker 映像。这在这里不起作用，但是让我们用它来设置一个远程服务器(比如 EC2 实例，或者数字海洋水滴):

要求:

*   (空)带有公共 IP 的 Ubuntu/Debian 远程机器
*   对计算机 root 或 sudo 访问权限

我们不需要对我们的 *frecklet* 做任何改变，我们需要做的只是改变我们命令中的`--target`:

```
$  frecklecute -t root@159.69.201.220 example-lxd.frecklet --from "a remote machine"
 ╭╼ starting run
│  ├╼ running frecklet: /home/markus/projects/frkl-dev/frkl.io/example-lxd.frecklet (on: 159.69.201.220)
│  │  ├╼ starting Ansible run
│  │  │  ├╼ updating apt cache
│  │  │  │  ╰╼ ok
│  │  │  ├╼ ensure rsync, ca-certificates and unzip packages are installed
│  │  │  │  ╰╼ ok
│  │  │  ├╼ creating freckles share folder
│  │  │  │  ╰╼ ok
│  │  │  ├╼ creating box basics marker file
│  │  │  │  ╰╼ ok
│  │  │  ├╼ recording python interpreter metadata
│  │  │  │  ╰╼ ok
│  │  │  ├╼ recording box metadata for later runs
│  │  │  │  ╰╼ ok
│  │  │  ├╼ Ensure nginx is installed.
│  │  │  │  ╰╼ ok
│  │  │  ├╼ Copy nginx configuration in place.
│  │  │  ├╼ reload nginx
│  │  │  │  ╰╼ ok
│  │  │  ├╼ write content to file: /etc/nginx/sites-enabled/localhost.http.conf
│  │  │  │  ╰╼ ok
│  │  │  ├╼ ensure user 'www-data' exists
│  │  │  │  ╰╼ ok
│  │  │  ├╼ write content to file: /var/www/html/index.html
│  │  │  │  ╰╼ ok
│  │  │  ├╼ geerlingguy.nginx : reload nginx
│  │  │  │  ╰╼ ok
│  │  │  ╰╼ ok
│  │  ╰╼ ok
│  ╰╼ ok
╰╼ ok $  curl http://159.69.201.220
<h1><i>freckles</i> says "Hello World", from a remote machine!</h1>% 
```