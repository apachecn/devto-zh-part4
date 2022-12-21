# 三星闪存指南(闪存 twrp，自定义操作系统，根，调整和一切你可以做的)

> 原文：<https://dev.to/azwyane/samsung-flashing-guide-flash-twrp-custom-os-root-tweaks-and-everything-you-can-possibly-do-16lk>

适用于所有三星设备

安装 twrp/自定义 rom/非 android rom/

适用于三星设备的所有变体

简洁的闪光指南

要求:

1.  自定义恢复-> twrp 恢复，将其下载到计算机上
2.  一个自定义的 rom，下载并保存在您的手机存储
3.  三星 odin，下载到电脑上运行应用

*下载 twrp:

首先从 twrp 官方网站找到 twrp 恢复镜像文件。为此，您需要了解手机的设备名称，例如三星 galaxy grand prime(sm-g530h)的设备代码名称是 fortuna3g，您可以在线搜索您的设备代码名称，或者如果您很难搜索，那么您可以简单地下载 vndnguyen 的名为 Phone info 的应用程序，然后在设备名称下查找。

要在您的设备上安装 twrp，您需要前往 [twrp.me](http://twrp.me/Devices) ，为您的特定设备下载 twrp。焦油文件，例如 fortuna3g.img.tar

*下载 odin

现在从[三星 odin 官方下载 Odin 工具](http://samsungodin.com/)现在运行 odin app 解压压缩包。

*在闪烁之前非常重要

转到您的手机通过进入设置- >关于- >设备并反复点击内部版本号直到开发者选项打开返回现在您会看到开发者选项已打开。向下滚动并启用 usb 调试，如果 oem 也存在，则解锁 OEM。然后关闭设备，同时按下 vol.down+home+power 按钮，直到显示 samsung written word 并释放它们，然后您将看到下载模式，然后按下 vol.up 键。现在使用 usb 将您的设备连接到您的计算机，并等待驱动程序自动安装。如果您的计算机没有自动下载，则您需要在线下载 samsung devices 驱动程序文件，否则如果它已经存在，则在 odin 中您将看到“已添加”写入。

*闪烁的 twrp

现在选择 AP 标签，选择 twrp.tar 文件，也可以看到面板，如果它说自动重启你需要取消选中它，不要玩其他标签。

现在你可以开始闪光了，在一个成功的闪光上你会看到写好了现在手动同时按 vol.up+power+home 转到 twrp，最后你会看到 twrp 出现。*在刷新自定义 rom
之前，将自定义 rom 文件存储在手机的内部或外部存储器中。做一个 nandroid 备份是安全的，以防出现任何问题。要做到这一点，请转到 twrp 中的备份选项，并在外部存储中进行备份，以确保安全。备份完成后，让我们继续下一步。

*自定义操作系统刷新

现在转到 twrp 中的擦除，转到高级擦除和复选框以擦除******系统、缓存、数据、dalvik Only******现在轻扫以擦除，在擦除成功后返回并转到安装，选择存储操作系统 zip 文件的存储器，选择 zip 文件并向右轻扫以进行刷新，并在成功闪烁后离开该进程向右轻扫以重新启动系统现在，您的系统加载可能需要大约 4/5 分钟。快乐闪光。

对人们:我知道有时你可能会因为一些文件格式损坏或其他原因而面临许多错误，我有解决这些问题的办法。根据你的要求，我很乐意在那里张贴解决方案，所以你可以在下面留下评论或给我发电子邮件。_x_

[![](img/fde7590b0296143ca37bb068badb3390.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oROxdqDP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/blogspot/sTChm/%257E4/1ikhpE7vtjk)