# Ubuntu 上的 Nvidia Optimus 亮度控制

> 原文：<https://dev.to/nbarrios1337/brightness-control-with-nvidia-optimus-on-pop-os-2b6p>

最近安装了 [Pop！_ OS](https://system76.com/pop)(Ubuntu 的衍生产品)在我的笔记本电脑的双启动设置中，我发现这几乎是一种无痛的体验，特别是因为预装了专有的 NVIDIA 驱动程序。

然而，我在我的安装中发现的一个问题是缺乏通过键盘控制亮度。有趣的是，在 Linux 上，按下`Fn+F3`或`Fn+F4`(对我来说，这通常会降低/增加 Windows 的屏幕亮度)没有任何作用，而音量控制(对我来说是`Fn+F9`和`Fn+F10`)没有出现任何问题。

发生了什么事？写完这个帖子，还是没有头绪。

# 修复背光问题

我发现的“修复”之一是为 X11 服务器添加一个配置文件，以便与我的集成英特尔显卡配合使用。我将概述如下，也许它会帮助别人更好的硬件支持。

我下载了 [`xbacklight`](https://linux.die.net/man/1/xbacklight) 到`apt`，并遵循了在[上找到的简短指南，它是 FOSS](https://itsfoss.com/fix-brightness-ubuntu-1310/) ，我将在下面概述它:

#### 注意:此修复适用于那些使用英特尔集成显卡的用户。

创建供 X 服务器使用的配置文件。
`sudo touch /usr/share/X11/xorg.conf.d/20-intel.conf`
在文本编辑器中打开它。
`sudo gedit /usr/share/X11/xorg.conf.d/20-intel.conf`
并在文件中添加以下内容:

```
Section "Device"
        Identifier  "card0"
        Driver      "intel"
        Option      "Backlight"  "intel_backlight"
        BusID       "PCI:0:2:0"
EndSection 
```

注销并重新登录(并可能重启您的系统)，然后`xbacklight`命令应该可以正常工作

## 奖金

尝试为`xbacklight -dec <percent>`和`xbacklight -inc <percent>`添加键盘快捷键。我重新创建了我的亮度控制来运行`xbacklight -10`和`xbacklight +10`，每次降低/增加 10%的亮度。

* * *

# 但是等等，为什么我的游戏运行的这么差？

事实证明，在这次修复后，我失去了使用我的 NVIDIA 显卡的能力。

在我完成上述修复后，我去玩了一些图形密集型游戏，我知道这些游戏在使用 NVIDIA 卡时可以正常工作，我发现它们运行速度很慢，几乎就像使用集成显卡一样。

但是`system76-power` (Pop！_OS 的图形切换器)显示我正在使用我的专用显卡。这是怎么回事？

通过终端打开`nvidia-settings`,显示一个错误(除了似乎无处不在的 GTK 错误)和一个空 GUI:

```
ERROR: Unable to load info from any available system 
```

这可不好，对吧？

现在，我不想安装其他图形转换器(即`bumblebee`、NVIDIA Prime)，因为试图替换`system76-power`包会导致整个桌面环境试图卸载自己，我还没有完全准备好生活在一个只有终端的 Linux 世界中。我还能做什么？

* * *

# 这是一种取舍

结果，你可能已经猜到了，包含 X 服务器的那个`20-intel.conf`文件阻止了我的 NVIDIA 卡以一种`system76-power`没有注意到的方式运行。

为了确保万无一失，我重新安装了发行版(谢天谢地，我已经把我的主目录设置在一个单独的分区中)并重复了我的步骤。然后，我从创建它的地方删除了`20-intel.conf`文件，并重新启动。

结果呢？我的显卡又开始工作了，我测试的游戏运行得完美无缺。但是除了使用系统状态菜单中的滑块之外，我不再能够调整亮度。

* * *

# 向前看

还有很多其他可能的修复，比如[覆盖亮度的内核参数](https://askubuntu.com/questions/428467/brightness-in-ubuntu-12-04)，这不是我太热衷于做的事情；我仍然需要几个抽象层来适应我的系统。然而，我将在这个 [StackOverflow 答案](https://askubuntu.com/questions/1034305/brightness-problem-ubuntu-18-04-lts)中尝试一些答案。也许 GRUB 编辑最终会为我工作。

让我知道你的想法，也许在评论中留下一些可能的修正！