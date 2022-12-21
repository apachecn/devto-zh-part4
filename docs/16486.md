# 为 Polybar 制作模块(Shell + Python)

> 原文：<https://dev.to/haideralipunjabi/making-modules-for-polybar-shell-python-3991>

### 背景

我在 2018 年 12 月开始使用 [Manjaro i3](https://manjaro.org/download/i3/) ，在 3 年的 Ubuntu 和一次安装 Arch 的失败尝试之后。作为一个长期潜伏在 [r/unixporn](https://reddit.com/unixporn) 的人，我知道我必须做什么。在学会了 Manjaro 的诀窍之后，我做的第一件事就是给我的装置装上蓖麻。

我最初安装了 [Polybar](https://github.com/polybar/polybar) 和我在[r/UNIX born](https://reddit.com/unixporn)上找到的一些配置。几周后， [u/adi1090x](https://reddit.com/user/adi1090x) [在 Reddit](https://www.reddit.com/r/unixporn/comments/ac5ggg/oc_polybar_themes_with_19_material_accent_colors/) 上发布了他美丽的 [polybar 主题](https://github.com/adi1090x/polybar-themes)。我立刻就爱上了(作为一个巨大的材料迷)并开始使用它们。5 个月后，我仍然使用他的主题的 v2。

在他的作品中，他用 [rofi](https://github.com/davatorium/rofi) 作为 polybar 的下拉菜单。很多人认为是我想出来的，但我第一次看到它是在 [u/adi1090x](https://reddit.com/user/adi1090x) 的作品中。

自从我使用 polybar 以来， [indicator-kdeconnect](https://github.com/Bajoja/indicator-kdeconnect) 对我不起作用。这是我日常使用的一个重要部分，我非常想念它。在想出没有办法和 polybar 一起使用它之后，我决定自己做一个，从而开始了我的 polybar 模块之旅。

### polybar-kdeconnect v1

我从学习 polybar 模块如何工作开始。任何可以在 shell 中执行的脚本(python、bash 等)都可以用作模块。脚本的输出显示在 polybar 中。Polybar 还允许在事件上执行脚本，如`click`、`right-click`等。

我的 kdeconnect 模块的 的一部分)来检查设备是否连接，并基于此显示图标的脚本。它不支持多种设备，唯一的方法是创建模块的副本。

在 Reddit 上分享之后，我收到了很多反馈、建议和想法来改进这个模块。

一天后，我为不同的电池电量设置了不同的颜色。

在进一步了解 polybar 模块(我将在最后链接到参考资料)之后，我准备再试一次 kdeconnect 模块。v2 与 v1 完全不同，以至于我不得不重写整个版本(大约 60 行)。)模块。

### polybar-kdeconnect v2

v1 之后的两个星期，我发布了 [polybar-kdeconnect v2](https://github.com/HackeSta/polybar-kdeconnect/tree/3e4e2df87b01b6f86773d0ab0e376c87f9b022f0) 。在众多改进中，以下是最重要的

*   不依赖 kdeconnect-cli
*   具有额外配置的多设备支持
*   与模块配对/解除配对
*   单一脚本

最好的改进是增加了多设备支持。它的工作原理是检查 kdeconnect 知道的所有设备，有三种类型

*   已连接(可到达且受信任)
*   未连接(不可达且受信任)
*   可用(所有剩下的)

根据它们的类型，脚本会将带有分隔符的图标附加到输出字符串中。检查完所有设备后，输出字符串将被打印出来(回显？？)并展示在 polybar 上。

为了让每个图标都可以单独点击，我使用了(代码中的%{A}东西)。

动作格式标签通过执行一些命令来工作。因为我不能直接通过名字调用函数，所以这个脚本没有加载到 action 标签使用命令的环境中，我必须找到我的脚本，然后调用函数并传递参数。

下面是附加到输出部分的内容

`devices+="%{A1:. $DIR/polybar-kdeconnect.sh; show_pmenu $devicename $deviceid $:}$icon%{A}$SEPERATOR"`

*注意，这不是我的最终代码的样子*

由于我对 shell 脚本没有太多经验，许多贡献者对我的代码做了一些修改(添加或删除引号等)。

有些问题还未解决，有些已解决，有些仍未解决。

为了修复代码中的问题，并遵循一些惯例，我在模块中添加了  。我一直想知道命令使用参数(-f，-x 等)的能力。如果你和我一样不知道如何实现它们，  就是答案。

为了防止错误，我用 Python 写了我的另一个模块。我更擅长 Python 而不是 Shell。我想过用 Python 重写，但是它对‘rofi’的严重依赖打消了我的想法。

[在 Github 上查看最新的 poly bar-kde connect](https://github.com/HackeSta/polybar-kdeconnect)

### polybar-browsermediacontrol

我通常用 [mpd](https://www.musicpd.org/) 听音乐，用 [mpd 模块听 polybar](https://github.com/polybar/polybar/wiki/Module:-mpd) 效果很好。在考虑使用 Python 编写 polybar 模块的同时，我考虑为浏览器制作一个媒体控件。它将非常类似于 mpd 模块。

第一个问题是向任何浏览器发送命令。同样，你需要在机器上安装某种服务器，向可安装的浏览器扩展发送命令。由于我没有这方面的经验，也不想为一个简单的任务写太多代码，我决定使用 [Plasma Browser Integration](https://community.kde.org/Plasma/Browser_Integration) ，这是对 kdeconnect 的一个很好的补充。

[Plasma 浏览器集成](https://community.kde.org/Plasma/Browser_Integration)允许用户使用 KDEConnect 从浏览器向 android 设备发送链接，还允许从 android 设备控制浏览器媒体。从技术上讲，它公开了一些 DBus 端点，KDEConnect 可以使用这些端点来控制浏览器的媒体，就像它控制其他媒体播放器一样。

我也在我的模块中使用相同的端点。

与 kdeconnect 相比，这个模块非常简单。我使用 [pydbus](https://github.com/LEW21/pydbus) 连接到 dbus 端点，使用 [argparse](https://docs.python.org/3/library/argparse.html) (python 相当于前面提到的 getopts)向脚本添加几个参数。

前面提到的再次用于制作播放/暂停按钮。

[在 Github 上查看最新的 polybar-browsermediacontrol](https://github.com/HackeSta/polybar-browsermediacontrol)

#### 重要资源

*   [主 Polybar Wiki](https://github.com/polybar/polybar/wiki)
*   [格式化 Polybar Wiki 页面](https://github.com/polybar/polybar/wiki/Formatting)
*   [用户贡献的多栏脚本](https://github.com/polybar/polybar-scripts)
*   rofi -一个窗口切换器，应用程序启动器和菜单替换-可以用作 polybar 的下拉菜单
*   [getopts](https://www.mkssoftware.com/docs/man1/getopts.1.asp) (Shell)
*   [argparse](https://docs.python.org/3/library/argparse.html) (Python)