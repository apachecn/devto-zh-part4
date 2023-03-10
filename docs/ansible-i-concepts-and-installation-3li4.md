# Ansible I:概念和安装

> 原文：<https://dev.to/hmartinezdev/ansible-i-concepts-and-installation-3li4>

联合内容！查看原文[“ansi ble I:概念与安装”@ hectormartinez.dev](https://hectormartinez.dev/posts/ansible-01-concepts-installation.html)

在这篇文章中，我们开始了一个关于 IT 自动化工具 Ansible 的系列。这是第一篇介绍 Ansible 及其安装的基本概念的文章。此外，我们将安装 Docker，这将有助于我们在更现实的场景中练习 Ansible，而无需退出我们的计算机。

在本文结束时，您将拥有:

*   学习了 Ansible 关于任务和节点的基本概念
*   在 Windows 上安装了 WSL 和 Ubuntu
*   安装的 Ansible
*   在您的计算机上安装 Docker

**注意**:这个系列主要集中在 **Windows** 上，但是命令可以很容易地移植到 Linux 上。如果你有任何疑问或问题，请提问！

## 三言两语就能回答

Ansible 是一个自动化工具，可以直接从你的电脑上运行机器**中的动作(又名命令或任务),无需任何特殊设置。我们用 **YAML** 来写动作，这降低了新手的门槛，因为它很容易读懂。Ansible 有一个很大的社区，背后有 RedHat，所以它每天都在增长，每天都在改进 Windows 支持(这对我来说很好，因为我在 Windows 环境中工作)。**

## 控制节点和被管理节点

有了 Ansible，你可以在其他*机器*上运行*你的电脑*的动作。从技术上讲，你应该将**控制节点**调用到*你的计算机*，将**管理节点**调用到其他*机器*。你的计算机控制机器，这些机器由你管理，非常简单。然而，这里我们将使用单词 **hosts** 来称呼被管理的主机，因为这个单词太长了(社区也使用单词 hosts，可能是出于同样的原因)。

## 完成工作:模块、任务、角色&行动手册

模块封装了可转换的“动作”。我们可以把它们看作工作单元。例如，使用`copy`模块([源](https://github.com/ansible/ansible/blob/devel/lib/ansible/modules/files/copy.py)和[文档](https://docs.ansible.com/ansible/latest/modules/copy_module.html))我们可以将文件从控制节点复制到主机。

模块需要参数来完成它们的工作。例如,`copy`需要知道我们想要将文件复制到哪里(在您的机器上)以及将被复制到哪里(在远程主机上)。我们可以提供使用`src`和`dest`的。例如:

```
# Welcome to YAML, use two spaces to ident
copy:  # Module name
  src: instructions.pdf  # Parameter, idented
  dest: some/remote/folder/instructions.pdf  # Parameter, idented 
```

仅仅这样是不行的，因为我们应该在**任务**中使用模块。这些基本上给了它们一个更好理解的名字，但是也可以用来控制执行的流程。比如:

```
name: distribute PDF with instructions to all machines  # Give them an meaningful name!
copy:  # Module name
  src: instructions.pdf  # Parameter, idented
  dest: some/remote/folder/instructions.pdf  # Parameter, idented 
```

任务应该在另一个更大的叫做**剧本**的东西里面运行。它包含按一定顺序排列的任务，并定义这些任务将在哪些主机上使用。一个剧本的例子:

```
# YAML files start with three dashes, weird but true
---
# Playbooks are lists, so use a dash before the name of the playbook
- name: Playbook Name
  hosts: all  # We want to run against all hosts
  # Tasks is also a list, so use a dashe before each task
  tasks:
    - name: distribute PDF with instructions to all machines  # Task name
      copy:  # Module name
        src: instructions.pdf  # Parameter, idented
        dest: instructions.pdf  # Parameter, idented

    - name: notify everyone  # Task name
      slack:  # Module name
        channel: event-X  # Parameter, idented
        msg: Instructions are in your computers!  # Parameter, idented 
```

这个剧本将运行`copy`模块，该模块将把`instructions.pdf`从我们的计算机复制到被管理的节点，然后调用`slack`模块来通知一个通道。

## 了解你的谈话对象:库存

那么我们的计算机如何知道哪些是被管理的节点呢？Ansible 有**库存**。清单是一个`ini`格式的文件，其中包含受管节点的列表。还记得剧本中的`hosts: all`吗？可以分组！比如:

```
[all]
managed-01.company.com
managed-02.company.com
10.120.240.240

[windows]
managed-01.company.com

[debian]
managed-02.company.com
10.120.240.240 
```

因此，如果你愿意，你可以使用`hosts: windows`或`hosts: debian`而不是`hosts:all`来运行你的剧本。

## 准备环境

### 安装 Windows Linux 子系统(WSL)

[微软 WSL 安装指南](https://docs.microsoft.com/es-es/windows/wsl/install-win10)

我们不能将 Ansible 直接安装到 Windows 中，但它可以在 Linux 的 Windows 子系统(从现在开始是 WSL)中工作，这是一种“嵌入”在 Windows 中的 Linux。

WSL 是一个特性，所以我们可以使用接口来激活它。但是，我们将使用 PowerShell 来激活它。使用管理员权限打开 PowerShell 提示符并运行:

```
Enable-WindowsOptionalFeature  -Online  -FeatureName  Microsoft-Windows-Subsystem-Linux 
```

重启计算机后，您可以从 Windows 商店安装 Linux 发行版。我个人使用 Ubuntu，因为我对它很熟悉。这里我将使用 Ubuntu 包管理器，但是你可以从其他发行版中找到类似的包管理器命令。

<figure>

[![Screenshot of Ubuntu in the Windows Store](img/6b79201e7a92d473483e146544de7d9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fhCHwGGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dev/asseimg/ansible-01/windows-store-ubuntu.png)

<figcaption>Ubuntu in the Windows Store.</figcaption>

</figure>

安装发行版后，在搜索栏中键入“Ubuntu”并打开应用程序。第一次您将不得不配置用户和密码，但这很简单。

<figure>

[![Screnshot of console configuring Ubuntu for the first time](img/72c08dcac894f73de349339992c996d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8S_4bwUT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dev/asseimg/ansible-01/ubuntu-configuration.png)

<figcaption>First time configuration of Ubuntu</figcaption>

</figure>

从现在开始，许多命令将在 WSL 内部运行，所以要小心！如果您在代码上看到一个`bash`标志，这意味着它在 WSL 内部运行，因为我们将在那里使用 bash。如果标志是`powershell`，我们将直接运行在 Windows
中。

### 准备 Python 虚拟环境并安装 Ansible

**注意** : *记得在发行版里面运行*

Ansible 与 Python 一起运行，因此您需要安装 Python。如果你选择了 Ubuntu 发行版，你将会安装`python3`。如果不是这样，请检查如何在您的发行版中安装 Python。然后试着跑:

```
python3 -m venv venv 
```

这将创建一个虚拟环境，我们将使用它来安装 Ansible。使用它，我们将保持我们的 Ansible 安装与已安装的全局包相隔离，这在存在冲突依赖时很好。在 Ubuntu 中，这将失败，并出现以下错误:

```
The virtual environment was not created successfully because ensurepip is not
available. On Debian/Ubuntu systems, you need to install the python3-venv
package using the following command.

    apt-get install python3-venv

You may need to use sudo with that command.  After installing the python3-venv
package, recreate your virtual environment.

Failing command: ['/home/hector/venv/bin/python3', '-Im', 'ensurepip', '--upgrade', '--default-pip'] 
```

所以运行:`sudo apt-get y install python3-venv`并接受建议的包的安装。之后，您应该可以毫无问题地运行命令:

```
python3 -m venv ansible-venv 
```

激活虚拟环境并安装 Ansible:

```
. ansible-venv/bin/activate
pip install ansible
ansible --help 
```

这应该会返回 Ansible 命令行帮助。如果可行的话，Ansible 可能会顺利运行。

### 安装对接器

所以在我们开始玩 Ansible 之前，现在是另一个安装。Docker 是一个工具，它使用容器来隔离操作系统之上的环境。把这些集装箱想象成真正的船舶集装箱，它们的内容彼此隔离，它们在一艘船(一台计算机)内，并且它们是标准化的。

<figure>

[![Screnshot of a table explaning the differences between Docker and Virtual Machines](img/d8282ed049e5dcf4c85a5f34378040f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qOSc_8KZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hectormartinez.dev/asseimg/ansible-01/docker-vs-virtual-machines.png)

<figcaption>Docker is a layer on top of the operating system, while virtual machines (VM) run inside over an hypervisor and fake even the operating system</figcaption>

</figure>

根据你对窗户的喜好，这里的道路会有一些不同。如果你有 Windows 10 **Home** 你将需要安装 **Docker 工具箱**。如果你有 Windows 10 **专业版、教育版或企业版**，你需要安装 **Docker 桌面**。如果您有 Linux，请查看您的发行版的 Docker 安装指南。

[Docker 通用安装概述](https://docs.docker.com/install/)

[Docker 工具箱安装](https://docs.docker.com/toolbox/toolbox_install_windows/)

【Windows 安装的 Docker

为什么会有这种差异？用于 Windows 的 **Docker 运行在 Hyper-V 上，这是 Windows 10 Home 中不可用的 Windows 功能。 **Docker 工具箱**运行在 VirtualBox 上，可以毫无问题地安装在任何 Windows 10 版本中。**

Docker 安装程序将为您完成艰巨的工作，如果有任何问题，请务必查看指南中的故障排除部分。安装后，你应该可以运行他们放在导轨中的`docker run hello-world`。

**注意** : *我们将从 Windows 端使用 Docker ( `docker`命令)，而不是 Linux 的 Windows 子系统。在 Linux 中，在终端中使用它，与 ansible 命令一样。*

## 关闭

所以现在你已经准备好开始玩 Ansible 和一些 Docker 容器了。您可以查看这两个工具的入门指南，以掌握更多的基本概念，并更好地了解如何使用它们。

在接下来的文章中，我们将构建一些 Docker 容器并配置 Ansible 来运行剧本。

## 继续阅读

*   [可行的基本概念](https://docs.ansible.com/ansible/latest/network/getting_started/basic_concepts.html)

*   [码头工人介绍](https://docs.docker.com/get-started/)