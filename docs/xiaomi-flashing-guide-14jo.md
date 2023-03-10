# 小米快闪指南

> 原文：<https://dev.to/azwyane/xiaomi-flashing-guide-14jo>

适用于所有小米设备

安装 twrp/自定义 rom/非 android rom/

对于小米设备的所有变体

简洁的闪光指南

要求:

1.  自定义恢复-> twrp 恢复，将其下载到计算机上
2.  一个自定义的 rom，下载并保存在您的手机存储
3.  ADB，将其下载到您的计算机上，并在命令提示符下运行
4.  解锁的引导加载程序

*下载 twrp:

首先从 twrp 官方网站找到 twrp 恢复镜像文件。为此，你需要了解手机的设备名称，例如小米 Redmi 4a 的设备代码名称是劳力士，你可以在网上搜索你的设备代码名称，或者如果你很难搜索，那么你可以简单地下载 vndnguyen 和名为 Phone info 的应用程序，然后在设备名称下查找。

要在您的设备中安装 twrp，您需要前往 [twrp.me](http://twrp.me/Devices) ，并为您的特定设备下载 img 文件，例如 rolex.img

*下载 ADB
现在运行 adb 终端解压 zip 文件。

*闪烁前非常重要

解锁小米的引导程序是做任何事情之前的一个复杂过程。为此你应该从 _ [_ **这里** _](https://en.miui.com/unlock/download_en.html) _ **下载小米解锁工具。按照 mi 提到的那样做，你的设备很容易解锁。** _

转到您的手机启用开发者选项，方法是进入设置->关于->设备，反复点击内部版本号，直到开发者选项打开。现在返回，您会看到开发者选项已打开。向下滚动并启用 usb 调试，如果 oem 也存在，则解锁 OEM。之后，关闭设备，同时按下 vol.down+power 按钮，直到显示 mi written word，然后释放它们，然后您将看到下载模式，现在使用 usb 将您的设备连接到您的计算机，现在键入:

```
adb devices 
```

Enter fullscreen mode Exit fullscreen mode

*(你会看到写有特定 id 的设备，如果写的是未授权，那么可能是你没有进行 usb 调试，或者可能是其他错误，如果你需要帮助，可以给我发消息)*

*再键入:*

```
adb reboot bootloader 
```

Enter fullscreen mode Exit fullscreen mode

*(此时你的设备进入引导模式)*

* flash twrp

现在选择 img 文件，粘贴到 c:/programs86x 下名为 minimal adb 和 fastboot devices 的文件中，粘贴到那里，现在输入:

```
fastboot devices 
```

Enter fullscreen mode Exit fullscreen mode

(检查设备连接)

现在，您可以通过键入:
来启动闪存

```
fastboot flash recovery theimgfile.img 
```

Enter fullscreen mode Exit fullscreen mode

在一个成功的闪光上，你会看到 done written now 手动转到 wrp，同时按 vol.up+power，最后你会看到 twrp 出现。

*在刷新自定义 rom

之前，将自定义 rom 文件存储在手机的内部或外部存储器中。做一个 nandroid 备份是安全的，以防出现任何问题。要做到这一点，请转到 twrp 中的备份选项，并在外部存储中进行备份，以确保安全。备份完成后，让我们继续下一步。

*自定义操作系统刷新

> 现在转到 twrp 中的擦除，转到高级擦除，并选中擦除******系统、缓存、数据、dalvik Only******

现在滑动以进行擦除，擦除成功后，返回并转到安装，选择存储操作系统 zip 文件的存储，选择 zip 文件并向右滑动以进行刷新，并在成功刷新后离开该进程向右滑动以重新启动系统现在，加载您的系统可能需要大约 4/5 分钟。快乐闪光。

对人们:我知道有时你可能会因为一些文件格式损坏或其他原因而面临许多错误，我有解决这些问题的办法。根据你的要求，我很乐意在那里张贴解决方案，所以你可以在下面留下评论或给我发电子邮件。_x_

[![](img/683c121f4852199f7bcbb5054810dff3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0bedBjvq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/blogspot/sTChm/%257E4/ud0gtt9SYq8)