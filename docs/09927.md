# 通过 SSH 将文件从 Windows 复制到 Linux

> 原文：<https://dev.to/bharatdwarkani/copying-a-file-from-windows-to-linux-through-ssh-5e8e>

如果你在 Windows 上工作，并且需要将一个文件从 Windows 传送到 Ubuntu 服务器，请遵循以下简单的步骤

**首先，在你的 Ubuntu 服务器上安装并配置 SSH**

执行以下命令:
$ sudo apt 更新
$ sudo apt 安装 openssh-server

在防火墙中为 SSH 启用端口 22
$ sudo ufw allow 22

检查状态 ssh 是否在 linux 机器上运行
$ sudo systemctl 状态 SSH

您可以使用以下命令启动或停止 SSH
$ sudo system CTL status SSH
$ sudo system CTL status SSH

**将文件从 Windows 复制到 Ubuntu 服务器**
执行此命令

scp file pathinindows username @ ubuntuserverip:linuxserverpath

示例
scp D:/txt file . txt root @ IP address:/home/usr/

传输时，cmd 提示会要求输入密码，输入你的 ubuntu 密码