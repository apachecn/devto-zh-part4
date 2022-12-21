# 对 VirtualBox 来宾使用原始 ZFS 卷

> 原文：<https://dev.to/mnohime/use-raw-zfs-volume-for-virtualbox-guest-45jg>

如果您曾经在 KVM、Xen、Hyper-V、VMware 或 VirtualBox 等平台上运行过虚拟客户机，您可能会认为连接到客户机的磁盘是一种特殊格式的映像文件(qcow2、VHDX、VMDK、VDI)，它位于主机文件系统的磁盘上。鲜为人知的是，这些平台中的大多数支持其他存储选项，即网络文件系统(NFS、SMB)、逻辑卷(分区)、磁盘，以及这里最值得注意的 ZFS 卷。

基于文件的安排对一些用户来说当然有价值。从我的经验来看，对于你想要快速创建和销毁的客人来说，或者当你除了简单的存储之外不太关心其他事情时，这是最方便的安排。

但是它也有缺点。这是其中之一。人们依赖于特定于虚拟化的设计，这些设计不能很好地从一个平台移植到另一个平台。作为一个试图将映像从 qcow2 转换为 VMDK 或 VHDX 的人，或者试图使用开源工具为 VMware 创建类似于即时 [JeOS](https://en.wikipedia.org/wiki/Just_enough_operating_system) 的虚拟机映像的人，我目睹了前一种情况下可能会以固定大小的映像结束，而在后一种情况下，映像只能从缓慢的虚拟 IDE 控制器启动。其中一些设计可能有[的致命缺陷](https://www.berrange.com/posts/2015/03/17/qemu-qcow2-built-in-encryption-just-say-no-deprecated-now-to-be-deleted-soon/)。

即使您认为您将坚持使用一个平台，并且您对平台互操作性不感兴趣，您也可能希望对您的客户使用您的操作系统本机工具。在 [OpenIndiana](https://www.openindiana.org/) 、an [illumos](https://www.illumos.org/) [发行版](https://illumos.org/docs/about/distro/)中，我们的系统组件如 [IPS](http://docs.openindiana.org/handbook/getting-started/#image-package-system-ips) (包管理器) [Zones](http://docs.openindiana.org/handbook/systems-administration/#zones) (操作系统级虚拟化/“容器】)和 [KVM](https://github.com/OpenIndiana/pkg5/blob/oi/doc/kvm.md) (硬件虚拟化)与高级文件系统和卷管理器 [ZFS](http://open-zfs.org/wiki/Main_Page) 很好地集成在一起，并可以利用其快照和加密等功能。

由于 OpenIndiana 上的这种集成水平，我们可以在一个区域中运行 KVM 客户机，从而使它与主机上的其他租户很好地隔离，控制其资源，等等。

有趣的是，VirtualBox 可以利用原始设备作为访客的存储。对于我们的目的，ZFS 体积将是一个理想的设备。首先，我将向您展示如何创建运行 ZFS 卷的 VirtualBox guest 虚拟机，然后我们将使用 ZFS 快照功能来保存 guest 虚拟机的状态，稍后我们将把 guest 虚拟机发送到另一个 ZFS 池，最后我们将从加密的 ZFS 卷运行 guest 虚拟机。

如果你想在你的系统上试试，这里有一些先决条件:`Host`ZFS 文件系统。我使用更新到最新包的 OpenIndiana 2019.04 [来获得 ZFS 加密](http://docs.openindiana.org/handbook/getting-started/#updating-packages)，但其他 Unices 可能也会这样做，但我没有试过。我有两个 ZFS 池:`rpool`是默认池，`tank`是我在一些例子中使用的池。需要安装带有 [Oracle VM VirtualBox 扩展包](https://www.virtualbox.org/wiki/Downloads)的 VirtualBox 并运行(我用的是 6.0.8 版本)。您还需要另一个带图形界面的系统，在那里您可以显示客人的图形控制台，除非您在您的`Host`上显示。

图例:`$`介绍用户命令，`#`介绍 root 命令。`Guest` / `Host` / `Workstation`表示执行命令的环境。

*我建议 VirtualBox 来宾小心写入原始设备，使用错误的卷可能会导致数据丢失。*

## 用 ZFS 卷创建客人

首先，以 root 身份创建 10 Gb 的`rpool/vboxzones/myvbox`卷:

```
Host # zfs create rpool/vboxzones
Host # zfs create -V 10G rpool/vboxzones/myvbox 
```

然后再次以 root 身份创建带有 SATA 控制器的 VirtualBox guest，为磁盘保留端口 1:

```
Host # VBoxManage createvm --name myvbox --register
Host # VBoxManage modifyvm myvbox --boot1 disk --boot2 dvd --cpus 1 \
--memory 2048 --vrde on --vram 4 --usb on --nic1 nat --nictype1 82540EM \
--cableconnected1 on --ioapic on --apic on --x2apic off --acpi on \
--biosapic=apic
Host # VBoxManage storagectl myvbox --name SATA --add sata \
--controller intelahci --bootable on
Host # VBoxManage storageattach myvbox --storagectl SATA --port 0 \
--device 0 --type dvddrive --medium OI-hipster-text-20190511.iso
Host # VBoxManage internalcommands createrawvmdk \
-filename /rpool/vboxzones/myvbox/myvbox.vmdk \
-rawdisk /dev/zvol/rdsk/rpool/vboxzones/myvbox 
```

最后一个命令创建了一个直通 VMDK 映像，该映像被附加到 guest 虚拟机，但实际上，*没有任何东西被写入这个文件*，而是直接使用了`/dev/zvol/rdsk/rpool/vboxzones/myvbox`卷。将图像贴在客人身上:

```
Host # VBoxManage storageattach myvbox --storagectl SATA --port 1 \
--device 0 --type hdd --medium /rpool/vboxzones/myvbox/myvbox.vmdk \
--mtype writethrough 
```

启动 guest 虚拟机，通过 RDP 客户端(Unix 上的 rdekstop 和 FreeRDP)连接到它:

```
Host # VBoxManage startvm myvboxdisk --type headless

Workstation $ xfreerdp /v:$(Host_IP) 
```

如果您看到了访客的引导程序，您可以继续从 ISO 映像安装访客，在安装程序的“Disks”屏幕中，您会看到您的磁盘:

[![alt_text](img/95d6dedd76efa0425b5ecc76ef7419cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLIyVm0c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/V3wWffn/disks2.png)

## 创建磁盘的快照

通过创建一个包含时间戳的文件来记录快照的大致时间:

```
Guest $ date > date 
```

现在，通过这种安排，我们可以创建卷的快照:

```
Host # zfs snapshot rpool/vboxzones/myvbox@first 
```

等待一段时间，重写时间戳:

```
Guest $ date > date 
```

关闭 guest 虚拟机，使用`-r`选项回滚到第*个*快照，这样 ZFS 就允许我们回滚到任何快照，甚至更旧的快照:

```
Guest # poweroff
Host # zfs rollback -r rpool/vboxzones/myvbox@first 
```

启动 guest 虚拟机，检查时间戳是否设置为第一个快照的时间戳:

```
Guest $ cat date 
```

万岁！我们演示了 VirtualBox guest 虚拟机如何处理 ZFS 快照。

## 将客户磁盘发送到另一个 ZFS 池

现在，让我们看看是否可以将来宾磁盘发送到另一个 ZFS 池，并从那里运行它。

```
Guest # poweroff
Host # zpool list
NAME    SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP  HEALTH  ALTROOT
rpool   118G  40.2G  77.8G      -       -   18% 34%  1.00x  ONLINE  -
tank    232G  22.2G   210G      -       -   1%  9%   1.00x  ONLINE  -
Host # zfs send rpool/vboxzones/myvbox | zfs receive -v tank/vboxzones/myvbox
receiving full stream of rpool/vboxzones/myvbox@--head-- into tank/vboxzones/myvbox@--head--
received 9.34GB stream in 153 seconds (62.5MB/sec) 
```

现在，“重新连接”直通磁盘，使其指向新的位置:

```
Host # VBoxManage storageattach myvbox --storagectl SATA --port 1 \
--device 0 --type hdd --medium emptydrive
Host # VBoxManage closemedium disk /rpool/vboxzones/myvbox/myvbox.vmdk \
--delete
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Host # VBoxManage internalcommands createrawvmdk \
-filename /rpool/vboxzones/myvbox/myvbox.vmdk \
-rawdisk /dev/zvol/rdsk/tank/vboxzones/myvbox
RAW host disk access VMDK file /rpool/vboxzones/myvbox/myvbox.vmdk created successfully.
Host # VBoxManage storageattach myvbox --storagectl SATA --port 1 \
--device 0 --type hdd --medium /rpool/vboxzones/myvbox/myvbox.vmdk \
--mtype writethrough
Host # VBoxManage startvm myvbox --type headless
Waiting for VM "myvbox" to power on…
VM "myvbox" has been successfully started. 
```

在启动时，来宾会注意到磁盘的设备 ID 与记录的 ID 不匹配，但会将其更正为我们实际拥有的 ID，并且在下次启动时不会发出警告:

[![alt_text](img/cbeb47aee83cc35f29cafbb9f81be0e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4G3-2QFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/q1KzR5b/vbox-hdd-removed3.png)

因此，我们证明了可以将来宾磁盘发送到另一个 ZFS 池，并从那里成功启动。

## 从加密卷启动 guest

现在，让我们看看来宾操作系统是否从加密的 ZFS 卷开始。

首先，创建一个加密的文件系统`tank/vboxzones_encrypted`，在提示符下输入一个密码:

```
Host # zfs create -o encryption=on -o keyformat=passphrase \
-o keylocation=prompt tank/vboxzones_encrypted
Enter passphrase:
Re-enter passphrase: 
```

然后将流发送到新的加密数据集:

```
Host # zfs send rpool/vboxzones/myvbox | zfs receive -v tank/vboxzones_encrypted/myvbox
receiving full stream of rpool/vboxzones/myvbox@--head-- into tank/vboxzones_encrypted/myvbox@--head--
received 9.34GB stream in 166 seconds (57.6MB/sec) 
```

并重新创建来宾传递磁盘以指向加密的数据集:

```
Host # VBoxManage storageattach myvbox --storagectl SATA --port 1 \
--device 0 --type hdd --medium emptydrive
Host # VBoxManage closemedium disk /rpool/vboxzones/myvbox/myvbox.vmdk \
--delete
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Host # rm /rpool/vboxzones/myvbox/myvbox.vmdk
Host # VBoxManage internalcommands createrawvmdk \
-filename /rpool/vboxzones/myvbox/myvbox.vmdk \
-rawdisk /dev/zvol/rdsk/tank/vboxzones_encrypted/myvbox
RAW host disk access VMDK file /rpool/vboxzones/myvbox/myvbox.vmdk created successfully.
Host # VBoxManage storageattach myvbox --storagectl SATA --port 1 \
--device 0 --type hdd --medium /rpool/vboxzones/myvbox/myvbox.vmdk \
--mtype writethrough
Host # VBoxManage startvm myvbox --type headless
Waiting for VM "myvbox" to power on…
VM "myvbox" has been successfully started. 
```

为了证明我们确实从加密卷运行，让我们手动从内存中卸载加密密钥(或者重新启动`Host`)并再次启动 guest】

```
Host # zfs unmount /tank/vboxzones_encrypted
Host # zfs unload-key tank/vboxzones_encrypted
Host # VBoxManage startvm myvbox --type headless
Waiting for VM "myvbox" to power on…
VBoxManage: error: Could not open the medium '/rpool/vboxzones/myvbox/myvbox.vmdk'.
VBoxManage: error: VD: error VERR_ACCESS_DENIED opening image file '/rpool/vboxzones/myvbox/myvbox.vmdk' (VERR_ACCESS_DENIED)
VBoxManage: error: Details: code NS_ERROR_FAILURE (0x80004005), component MediumWrap, interface IMedium 
```

不出所料，它失败了，因为加密的卷对于 VirtualBox 是不可读的。让我们解密它并再次启动来宾:

```
Host # zfs load-key tank/vboxzones_encrypted
Enter passphrase for 'tank/vboxzones_encrypted':
Host # VBoxManage startvm myvbox --type headless
Waiting for VM "myvbox" to power on…
VM "myvbox" has been successfully started. 
```

解密后，访客正确启动。

* * *

在这三个例子中，我们

1.  创建了一个快照，然后回滚到它，
2.  将来宾磁盘作为 ZFS 流发送到另一个 ZFS 池，并且
3.  启动了加密数据集的来宾。因此，我们证明了我们可以利用 VirtualBox 客户机中的操作系统级文件系统特性，这要归功于 VirtualBox 使用普通存储设备的未知能力。

## 担忧和未来方向

不幸的是，并不是一切都理想地工作。无论我如何尝试，原始 ZFS 卷的磁盘 IO 性能都明显低于 VDI 磁盘(或全局区域，但我在这里主要将其作为比较基准)的 IO 性能。

我在一个旧的[三星 840 PRO SSD](https://www.samsung.com/us/business/support/owners/product/840-pro-series-128gb/#fullspecs) (MLC)磁盘上运行了 sysbench 0.4.1 基准测试的 *fileio* 测试中的“seqwr”和“seqrd”测试模式，该磁盘似乎显示出一些磨损:

| 操作/存储类型 | 全局区域(主机) | VDI 磁盘(来宾) | ZFS 数据集(来宾) |
| 顺序写入(2Gb 文件) | 36，291 MB/秒 | 54，302 MB/秒 | 每秒 11，847 Mb |
| 顺序读取(2Gb 文件) | 195.63 MB/秒 | 565.63 MB/秒 | 155.21 MB/秒 |

有两件事值得关注:

1.  ZFS 卷的顺序写入速度比全局区域慢三倍，顺序读取速度比全局区域慢约 20%；
2.  VDI 磁盘性能实际上可以比全局区域中的性能更高(原文如此)。

第一个问题可能意味着不需要的`fsync()`发生在某个层，第二个问题可能意味着在将文件同步到虚拟机管理程序端的底层文件系统时发生了可疑的事情。也许`fsync()`在某种程度上被忽略了？(在任何情况下，这些数字都不能全信，它们只是用来说明各种存储类型之间的对比及其潜在的局限性。)

总之，事实证明，我们在各种 illumos 发行版上看到的 KVM 集成水平可以用 VirtualBox 来满足，因为我们似乎有必要的构建块。但是需要调查 IO 性能方面的罪魁祸首是什么，以及如何将分区添加到组合中，以便实现额外的租户隔离(特别是因为 VirtualBox 由于使用 ZFS 卷而作为 root 运行)。

鸣谢:Johannes Schlüter 的 [ZFS 和 VirtualBox](http://schlueters.de/blog/archives/137-ZFS-and-VirtualBox.html) 博客文章启发了我写这篇文章。