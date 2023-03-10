# 在 LUKS 分区上调整 LVM 的大小，而不会把一切弄乱

> 原文：<https://dev.to/4nduril/resize-lvm-on-luks-partition-without-messing-everything-up-14dk>

从那以后，我一直在全磁盘加密分区上运行我的工作电脑操作系统。目前这是 Manjaro Linux。当我设置我当前的机器时，我做了如下的分区方案:

```
sda                  238,5G  disk
├─sda1                 260M  part  /boot/efi
├─sda2                 128M  part  /boot
└─sda3                 237G  part
  └─tank               237G  crypt 
```

Enter fullscreen mode Exit fullscreen mode

在某个地方，我甚至不记得什么时候，我读到 1.28 亿英镑对`/boot`来说已经足够了。有好几年了。但是内核映像和/或 initram 磁盘变得越来越大，直到我无法再升级到新的内核。我运行的最后一个内核是 Linux 4.16，`/boot`中的文件占用了大约 75M 的空间，所以`mhwd-kernel -i linux417`在设备上剩下的空间太少了。

我需要做的是缩小`/dev/sda3`，将它移动到 SSD 的末端，并根据需要增加`/dev/sda2`。

但我不知道这是否可能与我的设置。在加密分区中有一个 LVM 容器，包含 5 个逻辑卷，包括`/`。我一次又一次地把它推到未来，因为大部分时间我都在运行项目，无法承受一台机器在<荒谬的时间内无法运行，在硬件即将改变之前，你永远也不会想到这个时间>。

但最终还是相对容易的。我担心在最坏的情况下，我将不得不重新设置我的整个机器，并恢复数据和系统分区的备份。这可能需要无休止的调整，直到它再次运行(不，我从来没有硬盘故障或类似情况，所以我从来没有实际做任何事情)。

所以，以下是我需要做的事情:

## 1。支持

列出所有逻辑卷:

```
# lvs
LV     VG   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
docker tank -wi-ao----   5,00g                                        
home   tank -wi-ao---- 100,00g                                        
mongo  tank -wi-ao----   1,00g                                        
root   tank -wi-ao----  25,00g                                        
swap   tank -wc-ao----  32,00g 
```

Enter fullscreen mode Exit fullscreen mode

对每个 lv 执行以下操作:

```
# lvcreate -s -n <name>snap /dev/tank/<name>
# dd if=/dev/tank/<name>snap of=/path/to/external/storage/<name>.img 
```

Enter fullscreen mode Exit fullscreen mode

其中`<name>`必须替换为 LV 的实际名称。然后我备份了`/boot`和`/boot/efi`分区，也备份了`dd`。
最后，我备份了加密分区的 LUKS 头:

```
# cryptsetup luksHeaderBackup /dev/sda3 --header-backup-file /path/to/external/storage/luks-header.bkp 
```

Enter fullscreen mode Exit fullscreen mode

## 2。从 u 盘启动实时系统并解密设备

```
# cryptsetup open /dev/sda3 tank --type luks 
```

Enter fullscreen mode Exit fullscreen mode

## 3。调整物理卷的大小

**注意:**我的 LVM 容器中有可用空间。正如你从上面`lvs`的输出中看到的，我目前只使用了大约 238GB 中的 143GB。这意味着我不必在调整包含物理卷的大小之前调整逻辑卷的大小*。如果您使用逻辑卷的所有可用空间，首先查看 [`lvresize(8)`](https://jlk.fjfi.cvut.cz/arch/manpages/man/lvresize.8) :例如在 [Arch Wiki](https://wiki.archlinux.org/index.php/LVM#Resizing_volumes) 中。*

我很大方的用:
把体积从 238,07G 缩小到 236G

```
# pvresize --setphysicalvolumesize 236G /dev/mapper/tank 
```

Enter fullscreen mode Exit fullscreen mode

## 4。调整加密设备的大小

找出当前大小是多少扇区(注意，我的加密设备与我的卷组同名:`tank`)。这可能与您的设置不同):

```
# cryptsetup status tank
...
sector size:  512
size:  499122176
... 
```

Enter fullscreen mode Exit fullscreen mode

最后，我想给`/boot`分区添加大约 1G。那就是`1024 * 1024 * 1024 / 512 = 2097152`扇区。

```
# cryptsetup -b 497025024 resize tank 
```

Enter fullscreen mode Exit fullscreen mode

## 5。调整 GUID 分区的大小

你看我们从最里面到最外面:LVM ->加密-> GUID。我使用`parted`来调整分区`/dev/sda3` :

```
# parted
(parted) unit s
(parted) print
...
Number  Begin     End         Size                     Name  Flags
...
3      3100672s  500115455s  497014784s               TANK  lvm 
```

Enter fullscreen mode Exit fullscreen mode

当我事后写这篇博客时，这些数字实际上是不同的。关键是 3 号分区一直到了磁盘的最后一个扇区，我现在必须计算它将来应该在哪里结束。因为`resizepart`不是以分区的未来大小，而是以分区的未来结束扇区作为参数。所以我从分区 3 ( `500115455`)的*结束扇区*中减去上面为 cryptsetup ( `2097152`)计算的相同扇区计数，得到`498018303`。

```
 (parted) resizepart 3 498018303s 
```

Enter fullscreen mode Exit fullscreen mode

现在，在主分区之后，SSD *上有了空闲空间。但是我想扩大分区 2。*

## 6。重新排序分区并调整分区 2 的大小

我是用 GParted 而不是命令行工具完成的。也许有一种方法可以用`gdisk`来实现，但是`parted`已经删除了对`move`分区的命令。因为我在一个图形化的实时系统中，而且也知道你可以用 GParted 来做这件事，所以我就去做了。
首先我关闭了加密设备，因为 GParted 不让我移动分区:

```
# vgchange -an tank
# cryptsetup close tank 
```

Enter fullscreen mode Exit fullscreen mode

然后我打开 GParted，右键点击加密分区。我选择了“改变大小|移动”,将空闲空间移动到分区之前的分区之后。然后我为`/boot`分区打开了同一个对话框，并扩展它以覆盖所有的空闲空间。最后，我提交了更改。