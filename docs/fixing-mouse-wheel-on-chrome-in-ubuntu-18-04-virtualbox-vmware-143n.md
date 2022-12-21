# 在 Ubuntu 18.04 Virtualbox / VMWare 中修复 Chrome 上的鼠标滚轮

> 原文：<https://dev.to/danvoyce/fixing-mouse-wheel-on-chrome-in-ubuntu-18-04-virtualbox-vmware-143n>

这已经让我疯了至少一年了。像许多开发人员一样，我运行一个虚拟机来维护我的日常开发环境(在很少的日子里，我实际上是在编码！).在 Ubuntu 18.04 下，Chrome 和其他应用程序从可爱的平滑滚动变成了可怕的混乱、参差不齐和脱节的滚动体验，这一直是一个令人烦恼的事情。

问题(结果)很简单- `libinput`不喜欢在移动鼠标的同时尝试操作鼠标滚轮。

解决方案表面上看起来很简单:用`evdev`替换`libinput`，所有问题都应该解决了。

不幸的是，Ubuntu 18.04 自带硬件支持层，这用 Canonical 提供的版本锁定和分层版本取代了许多标准组件。

免责声明:这在我的系统上有效——你可能会破坏你的系统！

第一步是删除这一层，转而安装 xorg:
的标准版本

```
sudo apt remove xserver-xorg-core-hwe-18.04 xserver-xorg-input-all-hwe-18.04 linux-generic-hwe-18.04 xserver-xorg-video-all-hwe-18.04 
```

现在安装 xorg 的标准版本和 evdev 驱动:

```
sudo apt install xserver-xorg-core xserver-xorg-input-evdev 
```

最后，您需要编辑 libinput 的 xorg 配置，并将驱动程序更改为`evdev` :

```
sudo vi /usr/share/X11/xorg.conf.d/40-libinput.conf 
```

并更改下面一行:

```
Section "InputClass"
        Identifier "libinput pointer catchall"
        MatchIsPointer "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput" 
```

致:

```
Section "InputClass"
        Identifier "libinput pointer catchall"
        MatchIsPointer "on"
        MatchDevicePath "/dev/input/event*"
        Driver "evdev" 
```

仅此一项就解决了“移动时滚动”的问题，立刻带来了不那么参差不齐的体验，然而对于一些严重的问题，你可能还想安装 im wheel([https://wiki.archlinux.org/index.php/IMWheel](https://wiki.archlinux.org/index.php/IMWheel)是一个很好的启动指南)

我的配置是:

```
None,      Up,   Button4, 2
None,      Down, Button5, 2
Control_L, Up,   Control_L|Button4
Control_L, Down, Control_L|Button5
Shift_L,   Up,   Shift_L|Button4
Shift_L,   Down, Shift_L|Button5

"^Navigator$"
None,      Up,   Button4, 2
None,      Down, Button5, 2
Control_L, Up,   Control_L|Button4
Control_L, Down, Control_L|Button5
Shift_L,   Up,   Shift_L|Button4
Shift_L,   Down, Shift_L|Button5

"^chromium$"
None,      Up,   Button4, 2
None,      Down, Button5, 2
Control_L, Up,   Control_L|Button4
Control_L, Down, Control_L|Button5
Shift_L,   Up,   Shift_L|Button4
Shift_L,   Down, Shift_L|Button5

".*gimp*"
Control_L, Up,   Control_L|Button4
Control_L, Down, Control_L|Button5
Shift_L,   Up,   Shift_L|Button4
Shift_L,   Down, Shift_L|Button5
Alt_L,     Up,   Alt_L|Button4
Alt_L,     Down, Alt_L|Button5
Control_L|Alt_L,     Up,   Control_L|Alt_L|Button4
Control_L|Alt_L,     Down, Control_L|Alt_L|Button5

".*nautilus*"
Control_L, Up,   Control_L|Button4
Control_L, Down, Control_L|Button5 
```

我真的希望这能帮助一些人，因为这对我来说是一个巨大的烦恼。