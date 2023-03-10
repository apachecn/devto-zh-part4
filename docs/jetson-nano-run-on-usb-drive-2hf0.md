# Jetson Nano —在 USB 驱动器上运行

> 原文：<https://dev.to/mrchoke/jetson-nano-run-on-usb-drive-2hf0>

### Jetson Nano —在 USB 驱动器上运行

#### 用自制 USB 引导 Jetson 纳米棒

[![](img/ec6669f8debc966a24d12ea96041fe2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uJjp0evi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AM9VeLZpfsPQSsnd3kEocvw.png)

如果有人用 Jetson Nano 就知道只有最初的 image 12g 了，但实际上它已经准备好了，但玩了一会儿，抓住了 nano，那就穿上它。

1.  将 USB 用作数据存储区。
2.  使用 USB 开机，但仍然需要 SD 来开机。

如果有人对使用 USB 的开机感兴趣，那么简单的家庭作业方法如下:

1.  下载 SD 图片จาก英伟达มา[https://developer . NVIDIA . com/embedded/learn/get-started-jetson-nano-dev kit # write](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#write)

2.  检查 USB

在这里，先检查一下您的 USB 是如何使用命令插入到设备中的。

lsblk

[![](img/33e299f6c47f15d78ffc700d5d14e692.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FxK-PLRH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/871/1%2Ak1hLWtPx4bLXdshgUcXBxw.png) 

<figcaption>列表中所有磁盘的</figcaption>

。

注意，如果是插入 Jetson Nano 的 SD，它将是 mcblk0，因此外部 USB 应该是 sda，其中完整路径将是
230。

```
/dev/sda 
```

1.  将影像向下推 USB

请使用以下命令 t0㎡

```
unzip -p **jetson-nano-sd-r32.2-2019-07-16.zip** | sudo dd of= **/dev/sda** bs=1M status=progress 
```

完成后，让 sda 对象退出
t0㎡以匹配下载的文件名称。

```
sudo eject /dev/sda 
```

1.  调整 sda 的大小

当我们将 USB 映像降级时，分区的大小与我们下载的映像大小相同，约为 12 G 的 Jetson Nano，但我们的 USB 更大。

```
sudo resize2fs /dev/sda1 
```

[t1](https://res.cloudinary.com/practicaldev/image/fetch/s--iDQJEu6X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/868/1%2AHOztSwF3oaM_pD_eKXiTMg.png)T3

<figcaption>前后再处理 2fs</figcaption>

。

1.  编辑引导加载程序

将引导加载程序编辑到 USB¨t0 }上。

```
sudo vim /boot/extlinux/extlinux.conf 
```

输入如下信息 t0㎡

```
TIMEOUT 30
DEFAULT usb

MENU TITLE L4T boot options

LABEL primary
 MENU LABEL primary kernel
 LINUX /boot/Image
 INITRD /boot/initrd
 APPEND ${cbootargs} quiet

LABEL usb
 MENU LABEL usb boot
 LINUX /boot/Image
 INITRD /boot/initrd
 APPEND ${cbootargs} rootfstype=ext4 root=/dev/sda1 rw rootwait 
```

1.  ฺReboot

应该是在 USB 上开机，如果没有，可以重新启动或移除 USB:p

[![](img/4c4043a137bf7a6783c6b14dc432fcec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MDsTOXxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2_ue7U1YWcZdc9a8RnG_PQ.png) 

<figcaption>开机后转 USB</figcaption>

1.  如果要切换到 SD，请编辑/boot/extlinux/extlinux。conf 在 SD 中，从 USB 上运行的系统，通过 df 查看山在哪里，先生。

* * *