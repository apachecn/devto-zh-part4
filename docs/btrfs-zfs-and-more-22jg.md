# BtrFS，ZFS，等等

> 原文：<https://dev.to/rkeene/btrfs-zfs-and-more-22jg>

## 黄油文件系统。不要敬酒。

我已经开始用 *BtrFS* 做实验，它的目标是在 Linux 上提供一个“先进和现代的文件系统”(与 ZFS 相比)。对于我的新工作站，我已经开始对我的主目录(`/home`)和我的构建目录(`/mnt/slackbuilds`)使用 BtrFS，以了解文件系统，并将其与 LVM 的 ZFS 和 EXT4 进行比较(我的所有其他数据，包括我的根磁盘都在 LVM 的 EXT4 上)。

我过去经常使用 ZFS，使用 BtrFS 有很大的不同，因为许多基本概念是不同的。BtrFS 没有“池”或“卷组”的概念，而是有“卷”。BtrFS 没有“数据集”或“逻辑卷”的概念，而是有“子卷”。

下面是 [ZFS](http://hub.opensolaris.org/bin/view/Community+Group+zfs/WebHome) 、 [BtrFS](https://btrfs.wiki.kernel.org/index.php/Main_Page) 和 [EXT4](https://ext4.wiki.kernel.org/index.php/Main_Page) 在 [LVM](http://sources.redhat.com/lvm2/wiki/) 的对比:

### 比较

|  | ZFS | btr 护堤 | EXT4 和 LVM |
| **涉及的命令** | zpool，zfs | mkfs、btr、btr | pvcreate、vgcreate、lvcreate、mkfs.ext4 |
| **磁盘池** | “zpool” | "音量" | "卷组" |
| **可安装单元** | "数据集" | “体积”和“子体积” | "逻辑卷" |
| **执照** | CDDL | （同 gramsperlitre）克/升 | （同 gramsperlitre）克/升 |
| **可以是引导文件系统** | 是 | 是(grub 2.00) | 不 |
| **可以是根文件系统** | 是 | 是 | 是 |
| **可以提供交换空间** | 是(zvols) | 不 | 是(lvm) |
| **已实施的操作系统** | Solaris、OpenSolaris、Nexenta、FreeBSD、Mac OS X、Linux | Linux 操作系统 | Linux 操作系统 |
| **稳定性** | 稳定的 | 稳定的 | 稳定的 |
| CLI-系统集成[1] | 强烈的 | 无力的 | 温和的 |
| **成长在线** | 是 | 是 | 仅当没有快照时 |
| **缩池** | 不 | 在线的 | 在线的 |
| **收缩文件系统** | 在线(减少配额) | 在线的 | 脱机的 |
| **更换磁盘(无奇偶校验)** | 是(必须是兼容大小的磁盘) | 是 | 是(复制磁盘上分配的空间) |
| **文件系统级存储池** | 是 | 是 | 不 |
| **重新平衡** | 不 | 是 | 可以手动完成(pvmove) |
| **校验和** | 是 | 是 | 不 |
| **自动更正校验和错误** | 是 | ？？？ | 不 |
| **压缩** | 是 | 是 | 不 |
| **重复数据删除** | 是(仅同步) | 是(仅异步) | 不 |
| **同上块** | 是 | ？？？ | 不 |
| **分层缓存** | 是 | 不 | 不 |
| **可写快照** | 是(克隆) | 是 | 是 |
| **写入时复制** | 快速、节省空间 | 快速、节省空间 | 速度慢，需要预分配 LV |
| **冗余度** | 镜像和奇偶校验(x1、x2、x3) | 反映 | 镜像，尽管 PVs 可以是 RAID 设备 |
| **最大体积大小** | 16eb | 16eb | 1 艾字节 |
| **最大文件大小** | 16eb | 16eb | 16tb |
| **最大快照数量** | 无限的 | 无限的 | 有效 32 |

[1]由于缺乏更好的术语——命令行界面与系统整体集成的程度，这可能是主观的。

更完整但不太集中的比较见[维基百科的文件系统比较](http://en.wikipedia.org/wiki/Comparison_of_file_systems)

### 罗塞塔石碑

1.  *任务:*从磁盘`/dev/A`、`/dev/B`和`/dev/C`创建存储池(条带化或线性串联)
    1.  使用 ZFS:
        1.  `# zpool create TESTPOOL A B C`
    2.  使用 BtrFS:
        1.  `# mkfs.btrfs -d raid0 /dev/A /dev/B /dev/C`
    3.  在 LVM 上使用 EXT4:
        1.  `# pvcreate /dev/A /dev/B /dev/C`
        2.  `# vgcreate TESTPOOL /dev/A /dev/B /dev/C`
2.  *任务:*使池中的存储可供系统使用
    1.  使用 ZFS:
        1.  `# zfs set mountpoint=/data TESTPOOL`
    2.  使用 BtrFS:
        1.  `# mkdir /data`
        2.  `# mount -t btrfs /dev/A /data`
        3.  更新`/etc/fstab`
    3.  在 LVM 上使用 EXT4:
        1.  `# mkdir /data`
        2.  `# lvcreate -L _SizeOfVolume_ -n DATA TESTPOOL`
        3.  `# mkfs -t ext4 /dev/TESTPOOL/DATA`
        4.  `# mount /dev/TESTPOOL/DATA /data`
        5.  更新`/etc/fstab`
3.  *任务:*向池中添加额外的磁盘
    1.  使用 ZFS:
        1.  `# zfs add TESTPOOL D`
    2.  使用 BtrFS:
        1.  `# btrfs device add /dev/D /data`
        2.  `# btrfs filesystem balance /data`
    3.  在 LVM 上使用 EXT4:
        1.  `# pvcreate /dev/D`
        2.  `# vgextend TESTPOOL /dev/D`
4.  任务:向文件系统添加额外的空间
    1.  使用 ZFS:
        1.  不需要任何操作
    2.  使用 BtrFS:
        1.  不需要任何操作
    3.  在 LVM 上使用 EXT4:
        1.  `# lvextend -L _SizeToIncreaseTo_ /dev/TESTPOOL/DATA`
        2.  `# resize2fs /dev/TESTPOOL/DATA`
5.  *任务:*从池中移除磁盘
    1.  使用 ZFS: *不适用*
    2.  使用 BtrFS:
        1.  `# btrfs device delete /dev/A /data`
        2.  `# btrfs filesystem balance /data`
    3.  在 LVM 上使用 EXT4:
        1.  `# pvmove /dev/A`
        2.  `# vgreduce TESTPOOL /dev/A`
6.  *任务:*更换工作磁盘
    1.  使用 ZFS:
        1.  `# zfs replace TESTPOOL A D`
    2.  使用 BtrFS:
        1.  `# btrfs device add /dev/D /data`
        2.  `# btrfs device delete /dev/A /data`
        3.  `# btrfs filesystem balance /data`
    3.  在 LVM 上使用 EXT4:
        1.  `# pvcreate /dev/D`
        2.  `# vgextend TESTPOOl /dev/D`
        3.  `# pvmove TESTPOOL /dev/A /dev/D`
        4.  `# vgreduce TESTPOOL /dev/A`
7.  任务:拍摄文件系统的快照
    1.  使用 ZFS:
        1.  `# zfs snapshot TESTPOOL@snapshot1`
    2.  使用 BtrFS:
        1.  `# btrfs subvolume snapshot /data /data/snapshot1`
    3.  在 LVM 上使用 EXT4:
        1.  `# lvcreate -s /dev/TESTPOOL/DATA -L _SizeToAllocate_ -n snapshot1`
8.  *任务:*回滚快照
    1.  使用 ZFS:
        1.  `# zfs rollback TESTPOOL@snapshot1`
    2.  使用 BtrFS:
        1.  *不确定...*
    3.  在 LVM 上使用 EXT4:
        1.  `# lvconvert --merge /dev/TESTPOOL/snapshot1`
9.  *任务:*删除快照
    1.  使用 ZFS:
        1.  `# zfs destroy TESTPOOL@snapshot1`
    2.  使用 BtrFS:
        1.  `# btrfs subvolume delete /data/snapshot1`
    3.  在 LVM 上使用 EXT4:
        1.  `# lvremove /dev/TESTPOOL/snapshot1`