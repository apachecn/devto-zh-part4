# Ansible II:剧本、容器和库存

> 原文：<https://dev.to/hmartinezdev/ansible-ii-playbooks-containers-and-inventory-3ie2>

联合内容！查看原文[“ansi ble II:剧本、容器和库存”@ hectormartinez.dev](https://hectormartinez.dev/posts/ansible-02-playbooks-containers-and-inventory.html)

欢迎来到 Ansible 系列！这是第二篇文章，我们将首先创建第一个 SSH Docker 容器来模拟一台远程机器，编写一个清单并构建一个简单的剧本来使用。

## 我们的第一个 SSH Docker 容器

在上一篇文章中，我们安装了 Docker。 **Docker for Windows** 面向拥有 *Windows 10 Pro 的人，企业或教育*和 **Docker Toolbox** 面向 *Windows 10 Home* 的人。所以我们应该在 PowerShell 终端中有`docker`命令。让我们旋转一些机器！

**注意**:如果在连接过程中，下面的命令被卡住或以包含*的错误结束，很可能是因为 Docker 没有正确启动。如果你有 **Docker 工具箱**，做一个`docker-machine restart`后跟`docker-machine env | Invoke-Expression`。这将重新启动 VirtualBox 计算机并刷新您的环境。*

这是一个基本命令，它将启动一个 Ubuntu 容器，标记为`19.04`，它将在启动时运行 Bash(`/bin/bash`)，它将是交互式的(`-i`)，它将分配一个伪 tty ( `-t`)，它将被命名为`ubuntu` ( `--name ubuntu`)，它将在退出后被删除(`--rm` ):

```
PS>  docker  run  --rm  -it  --name  ubuntu  ubuntu:19.04  /bin/bash  root@e491c2e0ccef:/# 
```

注意，第二行以`root@XXXXXX`开始，所以我们在容器内部。你可以做一些事情，比如`ls`或者`ps` :

```
root@e491c2e0ccef:/# ps
  PID TTY          TIME CMD
    1 pts/0    00:00:00 bash
   17 pts/0    00:00:00 ps
root@e491c2e0ccef:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var 
```

在这个容器中，我们只有两个进程在运行:`bash`，我们输入的控制台和`ps`，我们启动的那个。好了，我们现在可以使用`exit`退出容器，并启动一个已经安装并运行 SSH 的容器。

```
PS>  docker  run  --rm  --name  sshd  -d  -P  rastasheep/ubuntu-sshd:18.04  ...  PS>  docker  ps  --format  "table {{.Names}}\t{{.Status}}\t{{.Image}}"  NAMES  STATUS  IMAGE  sshd  Up  8  minutes  rastasheep/ubuntu-sshd:18.04 
```

我们可以使用下面的命令来检查 SSH ( `sshd`服务)是否正在运行:

```
PS>  docker  exec  -it  sshd  ps  -e  PID  TTY  TIME  CMD  1  ?  00:00:00  sshd  97  pts/0  00:00:00  ps 
```

### 连接 Docker for Windows

为了连接到新的 Docker 容器，我们想知道它的 IP。然而，这里我们有两种可能的场景: **Docker 工具箱**或 **Docker for Windwows** 。对于第二个你应该得到 IP 如下:

```
PS>  docker  inspect  --format  '{{ .NetworkSettings.IPAddress }}'  sshd  172.17.0.2 
```

那会是 Docker 容器本身的 IP，你可以直接连接。在这种情况下:

```
# ssh root@ContainerIP  ssh  root@10.0.75.13  # 'root' as password 
```

### 与 Docker 工具箱连接

对于使用 **Docker 工具箱**的场景，我们应该使用运行 Docker 容器的 VirtualBox 机器的 IP:

```
PS>  docker-machine  ip  192.168.99.100 
```

对我来说。如果我们尝试连接做`ssh root@192.168.99.100`我们将尝试连接到机器的 SSH 端口，而不是我们的容器。所以我们使用`-P`运行容器。这个标志将容器需要的端口映射到机器上，所以我们应该知道在我们的例子中哪个是那个端口:

```
PS>  docker  port  sshd  22/tcp  ->  0.0.0.0:32768 
```

Docker 容器的端口 22 被映射到 VirtualBox 机器的 32768。所以为了连接到容器，我们将做:

```
# ssh user@VirtualBoxMachine -p PortMapped  ssh  root@192.168.99.100  -p  32768 
```

现在您知道了如何使用带有 SSH 的 Docker 容器连接到我们描述的机器。

## 创建库存

现在是我们使用 Ansible 的时候了！我们需要一份清单和一本战术手册。我们将从创建库存开始。在这种情况下，它会非常小，因为我们只攻击一台机器。

我们可以在 IPs 之后或为每个组定义变量。我个人喜欢将所有变量放在清单之外，因为我通常会动态地创建它。但是，如果我们需要为每个 IP 指定端口，那么在这里就很方便。

同样，我们这里有每个 Docker 安装的场景。

**Docker 工具箱** :

```
[all]
192.168.99.100 ansible_port=32768 
```

**Windows Docker**:

```
[all]
10.0.75.13 
```

在 **Docker 工具箱** one 中，我们指定 Ansible 要连接的端口。然而，在 Windows 的 **Docker 中，端口是默认的 22，所以没有必要指定它。**

## 战术手册

现在是最后一部分的时候了:剧本。我们将做一个非常基础的，因为我们才刚刚开始，但是随着系列的发展，它们会变得更加复杂。目前，我们有这个:

```
---
- hosts: all
  vars:
    ansible_user: root
    ansible_password: root

  tasks:
    - name: ping
      ping: 
```

变量可以在剧本中定义，在称为`vars`的`hosts`级别的块中。剧本中的所有任务都共享这些，所以我们可以在任何地方找到它们。在我们的例子中，我们只需要告诉 Ansible 如何使用`ansible_user`和`ansible_password`连接到我们的机器。在这里硬编码这些不是一个好的做法，我们将看到一个更好的选择使用`ansible-vault`。目前，我们对它们进行了很好的编码。

为了运行剧本，我们使用`ansible-playbook` :

```
ansible-playbook -i inventory playbook.yml 
```

## 关闭

现在我们已经有了 Ansible 的基础，有了它我们就可以进入更复杂的场景。我们将重温这些主题，因为它们是 Ansible 的基础。在接下来的文章中，我们将开始我们旨在部署 web 服务的冒险，我们将学习 Docker Compose，并且我们将针对我们的机器发布多个剧本。不要错过它！

## 继续阅读

*   [可变模块索引](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html)
*   [剧本简介](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html)
*   [负责处理库存](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)