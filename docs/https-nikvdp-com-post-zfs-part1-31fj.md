# 零假设 ZFS:如何真正理解 ZFS

> 原文：<https://dev.to/nikvdp/https-nikvdp-com-post-zfs-part1-31fj>

这是关于 ZFS 的一系列文章中的第一篇，也是我希望在这里成为一个正在进行的系列的一部分:零假设。写这篇文章时，假设你对 ZFS 一无所知。

我对 ZFS 感兴趣已经有一段时间了，但是没有一个好的理由去用它做任何事情。上次我看的时候，Linux 上的 ZFS 还有点不成熟，但是在过去的几年里，Linux 对 ZFS 的支持已经大大加强了，所以我决定再试一次。不过，ZFS 有点自成一个世界，大部分资源会带您浏览一些快速命令，而不会解释 ZFS 背后的概念，或者假设用户非常熟悉传统的 RAID 术语。

# 背景

我在家里放了一台台式机(运行的是 Ubuntu 和 Arch Linux ),除了其他功能之外，它还可以充当存储/媒体服务器。我把多年来收集的照片和其他数码碎片保存在那里，如果它们消失了，我会非常难过。我每晚都通过优秀的 [restic](https://github.com/restic/restic) 到同样优秀的 [Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html) 备份所有东西，但是因为我在那里存储了数 TB 的数据，我没有遵循备份的基本原则:确保你可以从你的备份中恢复。因为在我的网络连接上测试需要几个月的时间，而且我害怕意外删除数据或驱动器故障，所以我决定增加一点冗余。

我的服务器现在有 3 个硬盘驱动器:一个 4TB 驱动器旋转磁盘驱动器，一个 2TB 驱动器旋转磁盘，一个 500GB SSD 驱动器保存根文件系统。我想保留的大部分数据都在 2TB 驱动器上，而 4TB 驱动器大部分是空的。在做了一些研究之后(阅读:在[/r/databoarder](https://www.reddit.com/r/DataHoarder/)上浏览帖子)，似乎人们用来添加透明冗余的两个最常用的工具是一个[snap raid](https://www.snapraid.it)+[mergerfs](https://github.com/trapexit/mergerfs)combo，或者旧的备用工具 [ZFS](https://www.freebsd.org/doc/handbook/zfs.html) 。

# 在 Linux 上安装 ZFS

如今，在 Linux 上安装 ZFS(假设您没有试图将它用作根文件系统)几乎简单得可笑。在 Ubuntu 16.04+上(可能还有最近的 Debian 版本)，这应该像下面这样简单:

```
sudo apt install zfs-dkms zfs-fuse zfs-initramfs zfsutils-linux 
```

> **说明:**
> 
> 为了简单起见，上面的命令安装了超出严格需要的内容:`zfs-dkms`和`zfs-fuse`是 ZFS 在 linux 上的不同实现，任何一个都足以单独使用 ZFS。之所以有多种实现，是因为 linux 的工作方式。`zfs-dkms`使用一种叫做 [DKMS](https://en.wikipedia.org/wiki/Dynamic_Kernel_Module_Support) 的技术(不出所料)，而`zfs-fuse`使用一种叫做[保险丝](https://github.com/libfuse/libfuse)的技术(更不令人惊讶)。FUSE 使得开发人员以牺牲一点性能为代价实现文件系统变得更加容易。DKMS 代表动态内核模块支持，通过这种方式，您可以安装模块的源代码，并让 linux 发行版自己负责编译该源代码，以匹配正在运行的 Linux 内核。

对于 Arch Linux，你需要使用 [AUR](https://aur.archlinux.org/) 并安装 [`zfs-linux`](https://aur.archlinux.org/packages/zfs-linux/) 。查看 Arch wiki 关于 ZFS 的文章以获得更详细的说明，但是对于大多数系统来说，这已经足够了:

```
sudo pacman -Syu zfs-linux 
```

# 规划您的驾驶

开始使用 ZFS 的第一步是弄清楚我想如何使用我的驱动器。出于这些目的使用 ZFS 的大多数人似乎会出去购买多个大硬盘，然后使用 ZFS 来镜像它们。我只是想在我已经有的驱动器上有更多的数据冗余，所以我决定对我的驱动器进行分区。

因为我有一个 2TB 的驱动器需要备份，所以我首先使用 [gparted](https://gparted.org/) 将我的 4TB 驱动器分成两个 2TB 的分区。然后，我在第二个驱动器上创建了 ext4 文件系统。

然后我用`blkid`和`lsblk`检查我的手工。这两个工具打印我的系统中所有“块设备”(读:硬盘)的列表，并显示在 Linux 中引用它们的不同方式:

```
$ blkid
/dev/sda1: UUID="7600-739F" TYPE="vfat" PARTUUID="ded30b23-f318-433c-bfb2-15738d42cc01"
/dev/sda2: LABEL="500gb-ssd-root" UUID="906bd064-2156-4a88-8d88-8940af7c5a34" TYPE="ext4" PARTLABEL="500gb-ssd-root" PARTUUID="cc6695ed-1a2b-4cb1-b302-37614cf07bf7"
/dev/sdc1: LABEL="zstore" UUID="5303013864921755800" UUID_SUB="17834655468516818280" TYPE="ext4" PARTUUID="072d0dd9-a1bf-4c67-b9b3-046f37c48846"
/dev/sdc2: LABEL="longterm" UUID="7765758551585446647" UUID_SUB="266677788785228698" TYPE="ext4" PARTLABEL="extra2tb" PARTUUID="1f9e7fd1-1da6-4dbd-9302-95f6ea62fff0"
/dev/sdb1: LABEL="longterm" UUID="7765758551585446647" UUID_SUB="89185545293388421" TYPE="zfs_member" PARTUUID="5626d9ea-01"
/dev/sde1: UUID="acd97a41-df27-4b69-924c-9290470b735d" TYPE="ext4" PARTLABEL="wd2tb" PARTUUID="6ca94069-5fc8-4466-bba2-e5b6237a19b7"

$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sdb      8:16   0   1.8T  0 disk
└─sdb1   8:17   0   1.8T  0 part
sde      8:64   0   1.8T  0 disk
└─sde1   8:65   0   1.8T  0 part
sdc      8:32   0   3.7T  0 disk
├─sdc2   8:34   0   1.8T  0 part
└─sdc1   8:33   0   1.8T  0 part
sda      8:0    0   477G  0 disk
├─sda2   8:2    0 476.4G  0 part
└─sda1   8:1    0   512M  0 part 
```

> **说明:**
> 
> 如果您不熟悉 Linux 如何处理硬盘，Linux 将硬盘称为“块设备”Linux 通过安装在`/dev`的虚拟文件系统提供对物理硬件的访问，根据硬盘的类型，硬盘通常采用`/dev/sdX`的格式，其中`X`是 Linux 分配给硬盘的字母 a-z。每个磁盘上的分区都被分配了一个编号，因此在上面的`lsblk`输出中，您可以看到磁盘`sdc`有两个分区，在输出中显示为`sdc1`和`sdc2`。
> 
> `blkid`命令显示传统的`/dev/sdX`标签，但也添加了`UUID` s，你可以把它想象成一个随机的 id，它总是指向那个特定的磁盘。这样做的原因是，如果你拔掉你的一个驱动器，并将其插入不同的端口，Linux 可能会给它一个不同的`/dev/sdX`名称，例如，如果你拔掉`/dev/sdc`驱动器，并将其插入另一个端口，它可能会变成`/dev/sda`，但它会保持相同的`UUID`。

我想将我的 2TB 驱动器转换到 ZFS，但由于我的宝贵数据目前都位于我的 2TB 驱动器上(`/dev/sdb1`)，我决定进行 swaparoo，首先将所有内容复制到我的 4TB 驱动器的第二个分区上(`/dev/sdc2`)，然后让 ZFS 接管原始分区(`/dev/sdb1`，并将数据复制回该驱动器。

我想要的最终结果是有一个带有两个“池”的布局(ZFS——驱动器集，稍后会详细介绍)。一个池应该包含我的原始 2TB 驱动器，并复制到我的 4TB 驱动器上的一个 2TB 分区。4TB 驱动器上额外的 2TB 分区将充当第二个池，这为我提供了良好的 ZFS 优势，如校验和以及拍摄驱动器快照的能力，以及以后添加另一个 2TB 驱动器/分区并镜像数据的选项。

如果你已经熟悉了`zpool`，这是完成的设置的样子:

```
$ sudo zpool status
pool: longterm
state: ONLINE
config:

       NAME        STATE     READ WRITE CKSUM
       longterm    ONLINE       0     0     0
         mirror-0  ONLINE       0     0     0
           sdc2    ONLINE       0     0     0
           sdb1    ONLINE       0     0     0

pool: zstore
state: ONLINE
config:

       NAME        STATE     READ WRITE CKSUM
       zstore      ONLINE       0     0     0
         sdc1      ONLINE       0     0     0 
```

# ZFS 术语和概念:镜像、条带、池、虚拟设备和奇偶校验

ZFS 引入了相当数量的新概念和术语，可能需要一些时间来适应。首先要理解的是 ZFS 实际上是做什么的。ZFS 通常使用驱动器池(因此得名于`zpool`命令)，并允许您对驱动器进行镜像或分条等操作。

你问镜像或条带化一个驱动器意味着什么？当镜像两个驱动器时，它们会协调一致地执行所有操作，因此写入一个驱动器的任何数据也会同时写入另一个驱动器。通过这种方式，如果你的一个硬盘出现故障，你的数据在另一个硬盘上仍然会安然无恙，而且通过 ZFS 称之为“重新镜像”的过程，如果你要安装一个新的硬盘来替换出现故障的硬盘，ZFS 会自动将你的所有数据同步回硬盘。

分条是另一回事。镜像驱动器对于冗余来说是很好的，但是有一个明显的缺点，那就是你只能使用一半的可用磁盘空间。有时情况需要相反的权衡:如果您购买了两个 2TB 驱动器，并且您希望能够使用所有 4TB 的可用存储，条带化将允许您这样做。在条带化设置中，ZFS 将数据“条带化”写入每个驱动器。这意味着，如果你写一个单一的文件，ZFS 实际上可能会存储部分文件在一个驱动器和部分文件在另一个。

这有很多好处:它通过使读和写并发来加速读和写。因为它在每个驱动器上存储一个文件的片段，两个驱动器可以同时写入，所以理论上你的写入速度可以加倍。读取速度也得到提升，因为你也可以同时从两个驱动器读取。所有这些速度和空间的缺点是您的数据不太安全。由于您的数据被分割在两个硬盘上，如果其中一个硬盘损坏，您可能会丢失所有数据-没有一个文件是完整的，因为虽然您完好的硬盘上可能有一半的文件，但另一半会随着您损坏的硬盘一起丢失。因此，实际上，您是在用接近两倍的速度和空间来换取接近两倍的丢失所有数据的风险。根据您正在做的事情，这可能是一个不错的选择，但是我不会将任何不想丢失的数据放入条带化设置中。

还有第三种类型，一种折衷的解决方案，即使用奇偶校验。这种类型的设置通常被称为 RAIDZ(或 RAIDZ2 或 RAIDZ3 ),介于完全条带化设置和镜像设置之间。这种方法使用所谓的奇偶校验磁盘作为一种半备份。这是由许多复杂的数学支持的，我不想假装理解，但它提供了一种在驱动器出现故障时恢复数据的方法。因此，如果您有三个 2TB 驱动器，您可以选择对它们进行条带化，但将其中一个专用于奇偶校验。在这种设置中，您有 4TB 的可用存储，但是如果一个驱动器发生故障，您不会丢失任何数据(尽管在更换故障磁盘之前，性能可能会非常糟糕)。就当是一种半备份吧。您也可以调整该比率，如果您将更多磁盘专用于奇偶校验，您可以在更多故障驱动器中存活而不丢失数据，这就是 RAIDZ2 和 RAIDZ3 中的 2 和 3 的含义。

> 更多关于 ZFS [这里](http://www.zfsbuild.com/2010/05/26/zfs-raid-levels/)和[这里](https://calomel.org/zfs_raid_speed_capacity.html)可以使用的不同 RAID 级别的信息。

现在，我们已经了解了驱动器阵列和 RAID 的高级概念，我们可以深入了解更多特定于 ZFS 的方面。首先要讨论的是 vdev 的概念。vdev 是一种“虚拟设备”，当`zpool` pools 驱动时，它会使用我们上面讨论过的 RAID 方法(条带化或镜像)之一来汇集这些虚拟设备的集合。然而，vdev 的用处在于，您可以将多个物理驱动器(或分区)放入单个 vdev 中。

虽然`zpool`可以通过 vdev 池创建条带化和镜像阵列，但 vdev 可以通过多组驱动器创建条带化或镜像阵列。这是 ZFS 如此灵活的部分原因。例如，您可以通过创建两个镜像 vdevss 来获得条带化设置的速度优势和镜像设置的冗余优势，其中每个 vdev 都配置为跨两个物理驱动器镜像数据。然后，您可以将两个 vdevs 添加到条带化池中，以获得条带化允许的快速读写，而不会在单个驱动器出现故障时面临丢失数据的风险(这实际上是一种相当流行的设置，在 ZFS 之外被称为 RAID10)。

这可能会很快变得非常复杂，但是[本文](https://pthree.org/2012/12/04/zfs-administration-part-i-vdevs/)(备份链接[此处](https://webcache.googleusercontent.com/search?q=cache:xwgzVPNhZ9kJ:https://pthree.org/2012/12/04/zfs-administration-part-i-vdevs/+&cd=1&hl=en&ct=clnk&gl=us)因为原始版本在撰写本文时已关闭)很好地完成了 vdevs 和 zpools 的各种可能排列。

# 做实验

ZFS 也可以在环回设备上使用，这是一个很好的方式来玩 ZFS，而不必投资大量的硬盘驱动器。让我们来看看一些环回设备的可能性，这样您就可以感受一下 ZFS 是如何工作的。

当 ZFS 使用另一个文件系统上的文件而不是直接访问设备时，它要求首先分配文件。我们可以使用 shell `for`循环通过使用`dd`命令将 1GB 的零复制到每个文件中(在运行该命令之前，您应该确保至少有 4GB 的可用磁盘空间):

```
for i in 1 2 3 4; do dd if=/dev/zero of=zfs$i bs=1024M count=1; done 
```

现在我们有了空文件，我们可以将它们放入 ZFS 池:

```
sudo zpool create testpool mirror $PWD/zfs1 $PWD/zfs2 mirror $PWD/zfs3 $PWD/zfs4 
```

> **注意:**上面的`$PWD`很重要，ZFS 在使用文件时需要绝对路径

现在你应该在`/testpool`安装了一个新的 zpool。用`zpool status`检查:

```
$ zpool status
  pool: testpool
 state: ONLINE
  scan: none requested
config:

        NAME                STATE     READ WRITE CKSUM
        testpool            ONLINE       0     0     0
          mirror-0          ONLINE       0     0     0
            /home/nik/zfs1  ONLINE       0     0     0
            /home/nik/zfs2  ONLINE       0     0     0
          mirror-1          ONLINE       0     0     0
            /home/nik/zfs3  ONLINE       0     0     0
            /home/nik/zfs4  ONLINE       0     0     0

errors: No known data errors 
```

您的新 ZFS 文件系统现在是活动的，您可以`cd`到`/testpool`将一些文件复制到您的新 ZFS 文件系统中。

# 下一步

我们已经讨论了 ZFS 的基础知识，在下一篇文章中，我们将继续讨论 ZFS 提供的一些更强大和更高级的功能，比如压缩、快照、`zfs send`和`zfs receive`命令，以及秘密的`.zfs`目录。

* * *