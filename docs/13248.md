# 使用 QEMU 而不拔掉你的头发

> 原文：<https://dev.to/djmoch/using-qemu-without-pulling-your-hair-out-o2a>

我习惯于仔细选择工具，并投入时间深入学习它们。QEMU 是我很久以来一直想学习如何使用的工具之一，但总是有点害怕。实际上，我已经能够通过 libvirt 间接使用它，但是我感觉用一个工具管理另一个工具是在欺骗我的规则。尽管我有一种模糊的负罪感，但事情还是这样继续下去，直到我读到了德鲁·德沃最近在 QEMU 上的一篇介绍性帖子。这篇文章写得很好(和往常一样)，你最好在继续之前读一读。关键是，这是我最终卷起袖子学习 QEMU 所需要的刺激。

掌握 QEMU 的过程比我预期的要痛苦得多，所以我想总结一下我在入门主题之外学到的一些东西。总的来说，艰难的教训与 QEMU 本身没有直接关系，但更多的是如何管理 QEMU 虚拟机。出于各种原因，我使用虚拟机来隔离环境，因此我需要自动管理它们的方法。特别是，我有两个需要，花了一些时间才满意地解决。

1.  在系统启动时自动启动虚拟机(并通过主机系统彻底关闭它们)。
2.  安全地远程访问虚拟机控制台。

让我们依次讨论这两个问题。

## 自动虚拟机管理

就我们的目的而言，我所说的虚拟机自动管理就是我上面所说的。如果我需要重启主机服务器，我希望虚拟机与主机系统一起彻底关闭，并在主机重启后自动恢复运行。因为这是 init 系统设计要做的事情，所以我们很自然地从那里开始设计我们的 VM 管理基础设施。

所以我们只需要告诉我们的 init 系统给 QEMU 发信号，关闭任何正在运行的 VM，我们就没事了，对吗？理论上是的，但实际上 QEMU 的管理界面在编写交互脚本时有点棘手。有一个 -monitor 开关，允许您配置一个非常强大的管理界面，您将需要使用它，因为默认情况下是将该界面连接到虚拟机本身的虚拟控制台(或者 stdio，如果您没有在本地运行图形界面)。配置显示器和它所连接的设备有几个选项，但是我在方便和安全之间找到的最佳折衷是通过 UNIX 套接字提供。

如果您已经阅读了 DeVault 的条目，那么您就会知道 QEMU 允许您通过命令行配置您想要的任何东西。在决定如何将监视器暴露给 init 系统(在我的例子中是 systemd)之后，剩下的工作很快就完成了。下面是我的服务文件的样子:

```
[Unit]
Description=QEMU virtual machine
Wants=network-online.target
After=network-online.target

[Service]
User=qemu
Group=qemu
UMask=0007
Environment=SMP=1
EnvironmentFile=/etc/conf.d/qemu.d/%I
ExecStart=/bin/sh -c "/usr/bin/qemu-${TYPE} -enable-kvm -smp ${SMP} -spice unix,disable-ticketing,addr=/run/qemu/%I.sock -m ${MEMORY} -nic bridge,br=${BRIDGE},mac=${MAC\_ADDR},model=virtio -kernel /var/lib/qemu/%I/vmlinuz-linux -initrd /var/lib/qemu/%I/initramfs-linux-fallback.img -drive file=/var/lib/qemu/%I/%I.qcow2,media=disk,if=virtio -append 'root=/dev/vda rw' -monitor unix:/run/qemu/%I-mon.sock,server,nowait -name %I"
ExecStop=/bin/sh -c "echo system_powerdown | nc -U /run/qemu/%I-mon.sock"

[Install]
WantedBy=multi-user.target 
```

%I 应该提示您这是一个[服务模板](https://www.freedesktop.org/software/systemd/man/systemd.service.html#Service%20Templates)，如果您计划将多个 VM 作为服务运行，这是一个很好的便利。这允许多个虚拟机通过符号链接使用同一个服务文件。例如，从 [qemu@webhost.service](//mailto:qemu%40webhost.service) 到 [qemu@的符号链接。服务](mailto:qemu@.service)将导致 systemd 用 webhost 替换% I。对服务模板的深入描述超出了本文的范围，但是上面的链接应该足以回答更多的问题。这里我要说明的最后一点是，ExecStop 中使用的 netcat (nc)实现必须是 OpenBSD netcat，否则服务不会干净地关闭。其他实现将在发送 system_powerdown 消息后立即断开连接，而 OpenBSD netcat 等待套接字关闭。

还值得花点时间强调一下上述服务模板中的 UMask 指令有多重要。QEMU 用它来为它创建的文件(包括套接字)设置权限，所以我们用它来保护我们的监视器和控制台套接字。0007 的 umask 指示 qemu 为 QEMU 用户和组创建任何具有完全权限的文件，但不提供全局权限。

那么缺少的就是环境文件，如下所示:

```
TYPE=system-x86_64
SMP=cores=1,threads=2
MEMORY=4G
MAC_ADDR=52:54:BE:EF:00:01
BRIDGE=br0 
```

环境文件的重点是要根据你的需求量身定制，所以不要只是盲目的复制这个。特别是，桥接设备需要存在，否则服务将失败。值得一提的是，我们使用了一个桥接设备，这样虚拟机就可以像它自己的机器一样出现在外界(因此我们可以将流量路由到它)。

关于自动化虚拟机启动/关闭的内容就说到这里，让我们来讨论一下如何访问控制台。

## 访问您的虚拟机控制台

同样，QEMU 有太多的选项来访问 VM 控制台，包括本地和远程。因为我在服务器上运行我的虚拟机，所以我想要允许远程访问的东西，但是我也想要相当安全的东西。UNIX 套接字最终再次成为一个好的中间解决方案。它们被视为文件，具有所有标准的 UNIX 权限，但也很容易通过 SSH 将流量从远程机器路由到 UNIX 套接字。

实现该配置的适用开关是 -spice 。在上面的服务模板中，您可以看到完整的开关如下所示:

```
-spice unix,disable-ticketing,addr=/run/qemu/%I.sock 
```

unix 配置 QEMU 使用 UNIX 套接字(与 TCP 端口相反)， disable-ticketing 配置控制台，不需要额外的密码(这没问题，因为我们依赖于 UNIX 文件权限)，addr 给出套接字路径。

现在，如果您想要远程访问控制台，只需通过 SSH 设置一个转发套接字并将您的本地 SPICE 客户端连接到它即可。下面是我为包装这种行为编写的 shell 脚本:

```
#!/bin/sh

uid=`id -u`
path=/run/user/$uid/qemu

if [! -d $path]
then
 mkdir -p $path
 chmod 700 $path
fi

ssh -NL $path/$1.sock:/run/qemu/$1.sock urbock.djmoch.org &
pid=$!

while [! -S $path/$1.sock]
do
 sleep 1
done
spicy --uri="spice+unix://$path/$1.sock"kill $!rm $path/$1.sock 
```

这就是我如何在不拔掉头发的情况下学会使用 QEMU 的。这是一个很棒的工具，我很高兴我花时间去学习如何使用它。建议你也这样做！