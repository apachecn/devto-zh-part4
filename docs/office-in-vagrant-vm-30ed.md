# 流浪乞讨人员办公室

> 原文：<https://dev.to/wincentbalin/office-in-vagrant-vm-30ed>

我想在虚拟机上使用办公软件，同时能够在主机上编辑文件。通常，人们在 [VirtualBox](https://www.virtualbox.org/) 中创建一个虚拟机，并使用[共享文件夹](https://www.virtualbox.org/manual/ch04.html#sharedfolders)将主机目录映射到这个虚拟机中。但是，因为这是一个漫长的过程，我决定使用[流浪者](https://vagrantup.com)来自动化它。

# TL；速度三角形定位法(dead reckoning)

1.  转到[这个 GitHub 库](https://github.com/wincentbalin/VirtualOffice)
2.  下载`Vagrantfile`并将其放入您的文档目录
3.  打开你最喜欢的命令行界面，进入那个目录
4.  运行`vagrant up`来配置虚拟机，并等待一段时间
5.  运行`vagrant rdp`并使用*流浪者*作为登录名和密码
6.  打开 LibreOffice 并配置您的个人数据，这样带有数据字段的文档就可以将它们设置为适当的值
7.  在主机系统上编辑您的文档！

# 设计决策

必须将`Vagrantfile`放入您想要编辑的文件所在的目录中。当然，我可以在主机操作系统上添加缺省的 documents 目录，但是我决定不这样做:首先，它会产生额外的维护负担(特别是当缺省操作系统路径的语法发生变化时)，其次，它不能在系统上工作，因为文档目录被移到了另一个位置。因此，目前同步目录的条目是

```
config.vm.synced_folder ".", "/home/vagrant/Documents", mount_options: ["dmode=775,fmode=664"] 
```

Enter fullscreen mode Exit fullscreen mode

流浪者创建的 VM 基于 Ubuntu Bionic x64。`Vagrantfile`安装软件包`xubuntu-desktop`和`libreoffice`。然后，它还通过安装`xrdp`，作为服务自动启动它，并启用端口 3389 来启用远程桌面连接。转发到端口配置有

```
config.vm.network "forwarded_port", guest: 3389, host: 33389, protocol: "tcp", auto_correct: true 
```

Enter fullscreen mode Exit fullscreen mode

通过将`xfce4-session`添加到文件`.xsession`中，可以自动启动 XFCE 会话。一切都以默认的流浪用户`vagrant`运行。