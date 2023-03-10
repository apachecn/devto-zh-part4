# 在 HP ProBook x360 上安装 Ubuntu Linux

> 原文：<https://dev.to/tylerlwsmith/getting-ubuntu-linux-installed-on-an-hp-probook-x360-11-2ei3>

上周，我从 Craigslist 上的一个家伙那里买了一台便宜的 HP ProBook x360，因为我想要一台 Linux 笔记本电脑。我曾计划清除整个驱动器，让它专门引导到 Ubuntu Linux。

在用 Ubuntu 18.04 LTS 制作了一个可引导 USB 后，安装程序在安装 GRUB2 时卡住了。在谷歌上搜索了几个小时后，我找不到任何在 HP ProBook x360 上成功安装 Linux 的人。事实上，相反我发现[惠普 ProBook x360 11 与 GRUB](https://lists.gnu.org/archive/html/bug-grub/2017-12/msg00010.html) 不兼容。

我很沮丧，用砖头砸了我的电脑，但我一直在戳它，直到我最终安装了 Ubuntu。这篇文章分享了我是如何做到的。我只在 Ubuntu 19.04 上测试了单引导，所以如果你尝试使用不同的发行版或双引导，你的里程数可能会有所不同。

## 安装 Ubuntu

你需要从从 Ubuntu 网站下载 Ubuntu 19.04 镜像开始，然后[在 Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0) 、 [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0) 、 [Ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0) 或其他 Linux 发行版上创建一个可引导的 USB。

当您的 HP ProBook 关闭时，插入可启动的 USB 驱动器，打开计算机，然后尽快按下`esc`键。如果 Windows 加载，你需要关闭电脑再试一次，更快地按下退出键。

你现在应该在一个选项屏幕。选择“F9 引导选项”。这将把你带到一个屏幕，在那里你可以从两个不同的名为“Windows 启动管理器”的选项中选择。选择第二个选项，如果 Windows 加载，关闭计算机并尝试第一个选项。

如果您只看到一个“Windows 启动管理器”，您需要确保您的系统可以从 USB 启动。回到主设置菜单，选择“F10 BIOS 设置”>【高级】>“引导选项”>“USB 设备引导”，并确保它已启用。

如果您选择了正确的 Windows 启动管理器，计算机将会黑屏很长时间。坐着别动。一段时间后，一些选项会出现在你的屏幕上。你想选择第一个选项，“不安装就尝试 Ubuntu。”如果几秒钟内你什么都不做，它会加载这个选项，所以当你阅读选项时，如果屏幕变成空白，不要惊慌。

一旦 Ubuntu 加载完毕，你的桌面上会出现一个安装 Ubuntu 的链接。启动安装程序。浏览选项。在“安装类型”屏幕上，选择“抹掉磁盘并安装 Ubuntu”警告:这将删除你硬盘上的所有内容。你*可能*能够通过与 Windows 一起安装并按照剩余的说明进行双重引导，但我没有尝试过这样做，所以如果你这样做，你可能会丢失所有的数据。没有备份不要做那样的事。

安装程序将启动，当它试图安装 GRUB2 时会失败。这完全没问题，也在意料之中。此时，它可能会迫使您重新启动计算机。如果是的话，直接启动 Ubuntu，不要再从闪存盘安装了。如果没有，就继续下去。

现在我们将[安装开机修复](https://help.ubuntu.com/community/Boot-Repair)。使用`ctrl alt t`(或使用应用程序菜单)打开终端，执行以下命令，每行后按 enter 键:

```
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt-get update
sudo apt-get install -y boot-repair && boot-repair 
```

Enter fullscreen mode Exit fullscreen mode

当启动修复弹出时，点击“推荐修复”选项。完成后，关闭电脑，拔出 u 盘，重新启动。计算机将加载到 GRUB 引导加载程序。要么选择“Ubuntu”，要么等几秒钟，Ubuntu 就会自动加载。您的计算机上应该有一个运行的 Linux 安装。恭喜你。

我希望这能为某人节省很多时间。让我知道这是否有帮助，或者如果你有问题；我是 Linux 新手但我会尽力回答！