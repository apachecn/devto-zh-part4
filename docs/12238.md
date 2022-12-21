# Grub4dos 引导 ISO 磁盘映像

> 原文：<https://dev.to/suntong/grub4dos-to-boot-iso-disk-image-57ho>

使用`grub4dos`启动 Ubuntu Ubuntu 18.04 ISO 磁盘镜像，这里是文件`MENU.LST`的入口:

```
title Ubuntu 18
  fallback 6
  find --set-root /iso/ubt1804.iso
  map --heads=0 --sectors-per-track=0 /iso/ubt1804.iso (0xff)
  map --hook
  root (0xff)
  kernel /casper/vmlinuz file=/cdrom/preseed/ubuntu.seed boot=casper iso-scan/filename=/iso/ubt1804.iso splash
initrd /casper/initrd.lz 
```

Enter fullscreen mode Exit fullscreen mode

NB，

*   对于 Ubuntu 64bit 从 14.04 到 17.10 使用了`vmlinuz.efi`，所以`kernel`必须是`/casper/vmlinuz.efi`，但是
*   对于旧版本和 18.04+版本，使用`/casper/vmlinuz`
*   对于 Ubuntu 32bit，一直是`vmlinuz`
*   对于较小的 ISO 磁盘映像，或者您的 RAM 非常大，您可以使用`--mem`的`map`命令将整个磁盘放入系统内存，就像这样:`map --mem /iso/smallcd.iso (0xff) || map --heads=0 --sectors-per-track=0 /iso/smallcd.iso (0xff)`

参考:

*   [https://ubuntuforums.org/showthread.php?t=2398574](https://ubuntuforums.org/showthread.php?t=2398574)
*   还有另外两个我已经[丢了](https://dev.to/suntong/hiccup-edit-will-always-bring-me-back-to-an-older-version-3had)。