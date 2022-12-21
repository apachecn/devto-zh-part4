# Grub4dos 指南——从 Linux 加载/安装 Grub4dos

> 原文：<https://dev.to/suntong/grub4dos-guide-loading-installing-grub4dos-from-linux-314j>

*这是[http://diddy.boot-land.net/grub4dos/files/install_linux.htm](http://diddy.boot-land.net/grub4dos/files/install_linux.htm)的缓存版本，是页面在格林威治时间 2019 年 7 月 1 日 11:11:19 出现时的快照，因为，截至今天 2019-07-21，网站的当前阶段是:*

```
This site can’t be reached 
diddy.boot-land.net’s server IP address could not be found.
DNS_PROBE_FINISHED_NXDOMAIN 
```

Enter fullscreen mode Exit fullscreen mode

原始文章开始:

### 通过 SysLinux 加载

要通过 Syslinux 加载，您首先需要将 Syslinux 安装到一个可引导设备上(版本 3.70 已测试)。将`C:\Grub4dos\GRUB.EXE`复制到包含您的 syslinux 文件的驱动器上的 Syslinux 文件夹，并向 Syslinux 配置文件`syslinux.cfg`添加一个条目。例如——

```
LABEL grud4dos
MENU LABEL Grub4dos
KERNEL grub.exe 
```

Enter fullscreen mode Exit fullscreen mode

或者，将`C:\Grub4dos\GRUB.EXE`复制到包含您的 Syslinux 文件的驱动器的根目录，并将以下条目添加到`syslinux.cfg`–

```
LABEL grud4dos
MENU LABEL Grub4dos
KERNEL /grub.exe 
```

Enter fullscreen mode Exit fullscreen mode

注意内核`/grub.exe`的使用。

### 通过(遗留)Grub 加载

已经安装了(遗留)GRUB 的 Linux 用户可以使用它来引导 Grub4dos。识别包含 GRUB 引导文件/文件夹的分区(例如`(hd0,0)/boot/grub`)。将`grub.exe`复制到 GRUB 引导文件夹。现在将下面的条目添加到您的`/boot/grub/menu.lst`文件中。

```
title Grub4dos
root (hd0,0)
kernel /boot/grub/grub.exe 
```

Enter fullscreen mode Exit fullscreen mode

更改`root (hd0,0)`命令以反映您自己的设置。

### 从 Linux 加载(kexec)

以下是使用 ubuntu Linux(版本 8.04 (Hardy Heron))测试的- kexec 包是使用终端命令`sudo apt-get install kexec-tools`安装的

`sudo kexec -l grub.exe`
T1】

### 安装 Grub4dos 代码到 MBR

要将 Grub4dos 代码安装到 MBR 中，请使用 bootlace.com。使用 Puppy Linux 版本 4.0，`(hd0)`被标识为`/dev/sda`——grub 4 dos 是使用命令`/mnt/sdb1/bootlace.com /dev/sda`安装的。在终端中运行该命令的输出-

```
# /mnt/sdb1/bootlace.com /dev/sda 

Disk geometry calculated according to the partition table: 
   Sectors per track=63, Number of heads=255 

Success. 
```

Enter fullscreen mode Exit fullscreen mode

### 将 Grub4dos 代码安装到分区引导扇区

以下说明是从`README_GRUB4DOS.txt` -
中复制的

```
******************************************************************************
***           Use bootlace.com to install partition boot record            ***
******************************************************************************

Since bootlace.com has not implemented the --install-partition option, you
need to use the already implemented --floppy=PartitionNumber option instead.

Hear is a way you might want to follow:

Step 1\. Get the boot sectors of the partition and save to a file MYPART.TMP.
    For NTFS, you need to get the beginning 16 sectors. For other type of
    filesystems, you only need to get one sector, but getting more sectors
    is also ok.

Step 2\. Run this:

    bootlace.com --floppy=Y --sectors-per-track=S --heads=H --start-sector=B --total-sectors=C --vfat --ext2 --ntfs MYPART.TMP

    where we suppose MYPART.TMP is for (hdX,Y) and the partition number Y
    should be specified as in the --floppy=Y option.

    Note that for FAT12/16/32/NTFS partitions, you can omit these options:

         --sectors-per-track, --heads, --start-sector, --total-sectors,
         --vfat and --ext2.

    For NTFS partitions, you must specify --ntfs option.

    For ext2 partitions, you can omit --vfat, --ntfs and --ext2 options,
    but other options should be specified.

Step 3\. Put MYPART.TMP back on to the boot sector(s) of your original partition
    (hdX,Y).

Note: Only a few file systems(FAT12/16/32/NTFS/ext2/ext3) are supported by now.

Note2: Under Linux you may directly write the partition. That is to say, Step
    1 and Step 3 are not needed. Simply use its device name instead of
    MYPART.TMP.

Note3: grubinst has the feature of installing grldr boot code onto a partiton
    boot area. 
```

Enter fullscreen mode Exit fullscreen mode

￼
将 Grub4dos 代码安装到`(hd0,1)`中，这是一个 NTFS 格式的分区，是设备`/dev/sda2`(在 puppy linux 版本中)

使用公式`(hdX,Y)`计算 bootlace.com 使用的`floppy=`参数。对于(hd0，1) Y=1，因此我们将使用`floppy=1`(为了安装到`(hd0,2)`，我们将使用`floppy=2`，对于`(hd1,3)`，我们将使用`floppy=3`，等等)。
使用 bootlace.com 命令`bootlace.com --floppy=1 --ntfs /dev/sda2`——希望您会收到类似下面的确认

```
# /mnt/sdb1/bootlace.com --floppy=1 --ntfs /dev/sda2 

Filesystem type is NTFS. 

Success. 
```

Enter fullscreen mode Exit fullscreen mode

将`grldr`复制到(hd0，1)并将(hd0，1)设为有效。`grldr`必须在安装引导扇区的分区上，`menu.lst`可以在另一个分区上。