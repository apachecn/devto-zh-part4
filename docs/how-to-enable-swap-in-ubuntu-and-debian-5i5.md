# 如何在 ubuntu 和 debian 中启用 swap？

> 原文：<https://dev.to/thecodersblog/how-to-enable-swap-in-ubuntu-and-debian-5i5>

##### 什么是交换空间？

任何操作系统中的交换空间都是指为从 RAM 中移走非活动页面而保留的磁盘空间量。在 linux 操作系统中，交换空间有专用交换和交换文件两种类型。这里我们将为 linux 系统创建一个交换文件。

> 对于本指南，我们假设系统没有交换分区。

##### 检查你的系统是否已经存在交换分区。

打开终端，输入以下命令进行检查。

```
swapon --show 
```

##### 检查空闲内存和磁盘。

```
free h
 //or
 df -h 
```

如果你的硬盘有足够的空间，那么让我们继续。

##### 步骤:1 关闭交换

```
swapoff /var/swapfile 
```

##### 第二步:调整交换文件大小

这里让我们增加到 2GB(假设现有的一个有 1GB 的空间，如文中所给)

```
dd if=/dev/zero of=/var/swapfile bs=1024k count=2000 
```

##### 第三步:使交换文件可用

```
mkswap /var/swapfile 
```

##### 第四步:制作 swapon

```
swapon /var/swapfile 
```

##### 第五步:将权限改为 0600

```
chmod 0600 /var/swapfile 
```

##### 第六步:验证交换文件

```
swapon -show 
```

##### 第七步:使交换文件永久化

```
echo '/var/swapfile none swap sw 0 0' | tee -a /etc/fstab 
```