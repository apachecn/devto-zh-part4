# Linux 中的文件创建时间

> 原文：<https://dev.to/anmolsarma/file-creation-time-in-linux-4i0d>

[`stat`](http://man7.org/linux/man-pages/man1/stat.1.html) 实用程序可用于检索 Unix 文件时间戳，即`atime`、`ctime`和`mtime`。其中，`mtime`的好处是显而易见的，它记录了文件最后一次被修改的时间。另一方面，记录文件最后一次被访问时间的`atime` <sup id="fnref:1">[1](#fn:1)</sup> 被称为[“也许是有史以来最愚蠢的 Unix 设计思想”](https://lore.kernel.org/lkml/20070804210351.GA9784@elte.hu/)。直觉上，人们可能期望`ctime`记录文件的创建时间。然而，`ctime`记录了文件的元数据被改变的最后时间。

通常，Unices 不记录文件创建时间。虽然一些单独的文件系统确实记录了文件创建时间 <sup id="fnref:2">[2](#fn:2)</sup> ，但是直到最近，Linux 还缺乏一个公共接口来将它们实际暴露给用户空间应用程序。结果，ext4 文件系统上的`stat` (GNU coreutils v8.30)的输出(它记录了创建时间)如下所示:

```
$ stat .
  File: .
  Size: 4096 Blocks: 8 IO Block: 4096 directory
Device: 803h/2051d Inode: 3588416 Links: 18
Access: (0775/drwxrwxr-x) Uid: ( 1000/ anmol) Gid: ( 1000/ anmol)
Access: 2019-06-23 10:49:04.056933574 +0000
Modify: 2019-05-19 13:29:59.609167627 +0000
Change: 2019-05-19 13:29:59.609167627 +0000
 Birth: - 
```

带有“`Birth`”的字段，本意是显示创作时间，体育令人沮丧的“`-`”。

事实上,`ctime`不是指创建时间，而是指更改时间，再加上没有真正的创建时间接口，确实会导致一些混乱。这种混乱似乎无处不在，以至于 Linux 内核中的`msdos`驱动程序[高兴地用 Unix 的变更时间来痛击](https://elixir.bootlin.com/linux/v5.1.14/source/fs/fat/inode.c#L883)!

当前`stat()`系统调用的局限性已经为人所知一段时间了。一个新的提供扩展属性的系统调用是[在 2010 年](https://www.spinics.net/lists/linux-fsdevel/msg33831.html)首次提出，新的 [`statx()`](https://lwn.net/Articles/685791/#statx) 接口最终[在 2017 年](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a528d35e8bfcc521d7cb70aaf03e1bd296c8493f)并入 Linux 4.11。花了这么长时间至少部分是因为内核开发人员很快遇到了计算机科学中最困难的问题之一:[命名事物](https://lkml.org/lkml/2010/7/22/249)。因为没有标准来指导它们，每个文件系统都用不同的名字来调用创建时间。 [Ext4](https://elixir.bootlin.com/linux/v5.1.14/source/fs/ext4/ext4.h#L744) 和 [XFS](https://elixir.bootlin.com/linux/v5.1.14/source/fs/xfs/libxfs/xfs_inode_buf.h#L40) 称之为`crtime`而 [Btrfs](https://elixir.bootlin.com/linux/v5.1.14/source/fs/btrfs/btrfs_inode.h#L187) 和 [JFS](https://elixir.bootlin.com/linux/v5.1.14/source/fs/jfs/jfs_incore.h#L46) 称之为`otime`。实现也有稍微不同的语义，JFS 只存储创建时间，精度为秒。

Glibc 花了一段时间为 statx()添加了一个包装器，并在 2018 年发布的[版本 2.28](https://www.sourceware.org/ml/libc-alpha/2018-08/msg00003.html) 中提供支持。快进到 2019 年 3 月，GNU [coreutils 8.31](https://lists.gnu.org/archive/html/coreutils-announce/2019-03/msg00000.html) 发布，`stat`终于获得了读取文件的支持创建时间:

```
$ stat .
  File: .
  Size: 4096 Blocks: 8 IO Block: 4096 directory
Device: 803h/2051d Inode: 3588416 Links: 18
Access: (0775/drwxrwxr-x) Uid: ( 1000/ anmol) Gid: ( 1000/ anmol)
Access: 2019-06-23 10:49:04.056933574 +0000
Modify: 2019-05-19 13:29:59.609167627 +0000
Change: 2019-05-19 13:29:59.609167627 +0000
 Birth: 2019-05-19 13:13:50.100925514 +0000 
```

* * *

1.  在现代 Linux 系统上使用 [`relatime`](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/power_management_guide/relatime) 可以减轻`atime`对磁盘性能的影响。<sup>【归来】</sup>
2.  对于 ext4，可以使用名为 [`debugfs`](https://linux.die.net/man/8/debugfs) 实用程序的`stat`子命令来获取文件的`crtime`。<sup>【返回】</sup>