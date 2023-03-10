# 如何:从主机 Ssh 到 Ubuntu VM Virtualbox

> 原文：<https://dev.to/ysellami/how-to-ssh-into-ubuntu-vm-virtualbox-from-host-machine-1kii>

大家好，在这篇文章中，我们将看到如何从主机系统连接到虚拟机

## 什么是 SSH？

SSH，Secure Shel 也称为 Secure Socket Shell，是一种网络协议，它为系统管理员提供了一种通过不安全的网络访问计算机的安全方式。

[![ysellami](img/bc08fc4dd6b19e26b6324ab460cda1ce.png)](/ysellami) [## 如何:在 VirtualBox 上安装 ubuntu

### yassine Sellami 1919 年 7 月 11 日 2 分钟阅读

#ubuntu #linux #virtualization #howto](/ysellami/how-to-install-ubuntu-on-virtualbox-562)

### 如何在 Ubuntu 上安装 openSSH 服务器

*sshd* :是 OpenSSH 服务器进程。它使用 SSH 协议监听传入的连接，并充当该协议的服务器。它处理用户认证、加密、终端连接、文件传输和隧道。
让我们开始安装 openssh-server。
先更新系统

```
$ sudo apt update
$ sudo apt upgrade 
```

Enter fullscreen mode Exit fullscreen mode

要安装 openssh-server 包，运行:

```
$ sudo apt install openssh-server 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装完毕，SSH 服务应该会自动启动。如有必要，您可以通过命令
手动启动(或停止、重启)该服务

```
$ sudo service ssh start 
```

Enter fullscreen mode Exit fullscreen mode

验证 ssh 服务正在运行

```
$ sudo systemctl status ssh 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/8e96f8bb9c64c44167d686fc6942a6c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sPLpov_a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c4ftfr693zcmlwj7bb5s.png)

### 配置防火墙并打开 22 号端口

在启用 UFW 防火墙之前，我们需要添加一个允许传入 SSH 连接的规则。如果你从一个远程位置连接到你的服务器，这几乎总是这样，并且你在明确允许进入的 SSH 连接之前启用了 UFW 防火墙，你将不再能够连接到你的 Ubuntu 服务器。

要配置您的 UFW 防火墙以允许传入的 SSH 连接，请键入以下命令:

```
$ sudo ufw allow ssh 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以通过键入:
来启用 UFW 防火墙

```
$ sudo ufw enable 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用以下命令检查 UFW 的状态:

```
$ sudo ufw status 
```

Enter fullscreen mode Exit fullscreen mode

## SSH 到虚拟机 VirtualBox

登录访客 Linux VirtualBox 虚拟机的最佳方式是[端口转发](https://www.virtualbox.org/manual/ch06.html#natforward)。默认情况下，您应该已经有一个使用 NAT 的接口。
然后转到网络设置并点击高级，点击端口转发按钮。
[![](img/7420dace36b3cde1e1e865b4bf60b22a.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--_4duOz5k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jejr1b3542qqkx9pw1f8.png)

添加新规则。As 值:
rule name = "ssh "，" Protocol' = "TCP "，" Host port" = 2222，" Guest port" = 22。
VirtualBox 将创建一个专用网络(10.0.2.x ),该网络将使用 NAT 连接到您的主机网络，要获取访客 IP，请在虚拟机 ubuntu 中键入以下命令:

```
$ sudo ifconfig [interface_name] (for me:  ifconfig enp0s3)
if use latest version of ubuntu, it use #Netplan, You can try this command
$ sudo ip a 
```

Enter fullscreen mode Exit fullscreen mode

规则的其他内容可以留空。
[![](img/89f070b98cb76ae4b428fb995a027b5b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--uFrhTDxh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tqreaxpi5w1igvnfn1yk.png)

通过这种方式，我们可以将 [putty](https://www.putty.org/) 或 [MobaXterm](https://mobaxterm.mobatek.net/) 指向 127.0.0.1 的端口 22，VirtualBox 会将此连接重定向到我们的虚拟机，其 ssh 守护进程将在那里应答它，允许我们登录。
在我的例子中，我使用 MobaXtrem，通过 SSH 进入客户虚拟机，编写:

```
$ ssh -p 2222 [user]@127.0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

*其中 user 是您在虚拟机中的用户名。
[![](img/9656e3e1fc17943de62d4eac8cfa749e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--bRBqrNeF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uzh50o7jr1ire4vxlveu.png)

## 关闭 virtualbox 中的 GUI 会话，而不关闭虚拟机

在开始按钮旁边有一个额外的下拉菜单，还有一个更好的选项:
可拆卸开始！这是一个混合启动选项，在后台运行虚拟机进程，只显示一个您可以轻松关闭的无头虚拟机客户端。
[![](img/e1317d01c67162842691495a85e84693.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Ta5-n_5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8066e5pbgju7124vb4vi.png)

然后你可以关闭 VirtualBox GUI，通过选择“在后台继续瑞宁”:
[![](img/2daf3c934f0098e1c27f6855a836d289.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zwY0uyY4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w5awy2m3b1avtd4cfbae.png)

享受你现在可以 SSH 到你的虚拟机的事实吧！

[![ysellami](img/bc08fc4dd6b19e26b6324ab460cda1ce.png)](/ysellami) [## 如何:使用 Wine 在 Ubuntu (Linux)上运行 MobaXterm

### yassine Sellami Sep 3 2 分钟读数

#tutorial #ubuntu #linux #howto](/ysellami/how-to-run-mobaxterm-on-ubuntu-linux-with-wine-ohf)[![ysellami](img/bc08fc4dd6b19e26b6324ab460cda1ce.png)](/ysellami) [## RHEL / Ubuntu 有用的命令

### yassine Sellami Sep 3 2 分钟读数

#systems #linux #cloud #devops](/ysellami/rhel-ubuntu-useful-command-433f)