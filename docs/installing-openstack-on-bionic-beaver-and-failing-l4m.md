# 在仿生海狸上安装 OpenStack 失败

> 原文：<https://dev.to/ai101gh/installing-openstack-on-bionic-beaver-and-failing-l4m>

为了好玩，我尝试在全新安装的 Ubuntu 18.04 上运行 Open Stack 的工作站安装。我只是想玩，所以我认为本地主机安装会很简单。

不幸的是，它并不直截了当，因为我遵循了错误的指示。

tldr 如果您想在 Ubuntu 18.04 上安装 OpenStack workstation，请遵循以下说明:[https://www . journal dev . com/30037/install-open stack-Ubuntu-dev stack](https://www.journaldev.com/30037/install-openstack-ubuntu-devstack)

相反，我尽职尽责地遵循这些指示:[在 Ubuntu 上安装 open stack](https://ubuntu.com/openstack/install#workstation-deployment)

直到我完成了变出步骤的一部分，它才建议我检查命令的输出:

> 无法解析来自 LXD 的 JSON 输出，那么/snap/bin/lxc query-wait-X GET/1.0 会返回关于 LXD 服务器的信息吗？

运行命令 **sudo** 工作正常。经过大量的搜索，我发现我需要运行以下:

```
$ sudo usermod -a -G lxd $USER 
```

Enter fullscreen mode Exit fullscreen mode

然后，我需要注销并再次登录，以使更改生效，尽管有更聪明的方法可以做到这一点。(注意，我还寄了一张票给 Canonical，希望他们能修改指令)。

当我再次运行变戏法时，我得到了另一个错误:

> 等待 6 个 kube 系统舱启动

它就这样坐了一个多小时，所以我变得暴躁和退出。我想我会变戏法似的变下去，重新开始。我决定我也要做一个 **lxd 初始化**。很难找到如何做到这一点，但这篇文章很有帮助:[如何再次初始化 LXD](https://blog.simos.info/how-to-initialize-lxd-again/)

所以回到尝试运行 **lxd init** 的问题。注意，根据你召唤出的魔法数量，你可能不需要运行所有这些步骤:

```
$ conjure-down # This command will likely fail
$ lxc list 
# Delete all containers created by juju …
$ lxc list | grep juju | cut -f2 -d"|" | while read ii; do lxc stop $ii; lxc delete $ii; done
$ lxc network delete lxdbr0
$ lxc profile list | grep juju | cut -f2 -d"|" | while read ii; do printf 'config: {}\ndevices: {}' | sudo lxc profile edit $ii; done
# Note you may have to run the above command on other created profiles.
$ lxc storage delete default
$ sudo juju list-controllers
$ sudo juju destroy-controller name-of-the-controller-to-destroy
# You may want to take a copy of the following directory before running the rm command …
$ rm -rf ~/.local/share/juju 
```

Enter fullscreen mode Exit fullscreen mode

这让我又回到了正轨。在 **lxd init** 期间，我最初为存储后端选择了“zfs”，为 IPv6 选择了“auto”。但是在发现[安装了具有召唤功能的单服务器 OpenStack】后，我意识到我应该将池存储设置为“dir”并将 IPv6 CIDR 设置为“none”。](https://tutorials.ubuntu.com/tutorial/install-openstack-with-conjure-up#0)

所以我再次运行**召唤**...它再次失败，抱怨无法连接到 10.x.x.x 地址。

我在某处读到过它不喜欢“自动”IPv4 CIDR，所以我选择了一个宽度为 24 的地址...它又失败了

> 一些应用程序未能成功启动。

那么，下一个合乎逻辑的步骤是哪些以及为什么。我看~/。cache/mage-up/mage-up . log 并找到一个孤独的行:

> 错误:根:neutron-gateway/0 失败:工作负荷状态为错误

我倒下了，但没有被打败。我想我需要休息一下，过一会儿再回来。