# 从 USB 驱动器运行 Ubuntu

> 原文：<https://dev.to/angelarae63/ubuntu-distrubution-233i>

嘿，伙计们！我今天尝试在我儿子的笔记本电脑上用闪存盘运行 Ubuntu，经历了相当大的冒险，所以我想分享一下我在这里学到的东西，以防你们中的一些人想尝试一下。我目前在编程课上使用的笔记本电脑是华硕 ROG 游戏笔记本电脑。它配有英特尔 i7 处理器、16 gb 内存和 1TB 硬盘。还有超过 250 gb 的剩余空间，所以，理论上，我可以在这台机器上运行 Ubuntu，作为当前 Windows 10 操作系统的双重启动，但这需要我对驱动器进行分区，这是我以前没有尝试过的，所以我决定，因为这不是我的电脑，我会选择侵入性较小的选项。

实现这一点的步骤如下:

1.  [下载 Ubuntu](https://ubuntu.com/download/desktop)
2.  [下载 Rufus](https://rufus.ie/) 并创建一个可引导驱动器。
3.  在 Windows 控制面板中编辑电源按钮设置
4.  将 Ubuntu 复制到闪存盘。
5.  编辑系统 Bios
6.  运行 Ubuntu

**下载 Unbuntu** 根据您的系统和网络，这可能需要 30-45 分钟。

接下来，**下载鲁弗斯**。您将使用 Rufus 创建您的可引导 USB 驱动器。我发现[这些来自 Ubuntu 网站的说明](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows?_ga=2.178864676.1005760627.1561695937-212263140.1561695937#0)非常有帮助。按照它们来创建您的可引导驱动器。

接下来，你需要[改变电源按钮](https://www.windowscentral.com/how-disable-windows-10-fast-startup)的设置。确保在改变系统 Bios 之前完成这个步骤**。**

一旦 Ubuntu 下载完成，你需要改变你的系统 bios，首先从你的 USB 驱动器**引导，然后从 Windows 操作系统引导。在 Windows 10 中做到这一点比以前的 Windows 版本复杂得多。
你必须去设置标签来完成这个。**

```
Select Settings at the Windows menu
Select Update & security.
Select Recovery from the left menu.
Click Restart Now under Advanced startup. ...
Click Troubleshoot.
Click Advanced options.
Select UEFI Firmware Settings. ...
Click Restart. 
```

您的计算机现在应该启动到 Bios。确保在 Bios 启动时插入闪存驱动器。不同的 Bios 可以以不同的方式运行，但在我的系统上，我可以选择引导选项卡并更改计算机的引导顺序。因此，现在这台笔记本电脑将首先从 USB 驱动器启动，然后是 Windows 10 操作系统所在的 C:驱动器。

现在，用 u 盘重启你的电脑。将加载 Ubuntu 菜单。选择“不安装就试用 Ubuntu”一个可用版本的 Ubuntu 将加载！

太棒了。我终于开始使用 Ubuntu 了，我喜欢它的速度。你们中的许多人在[这篇文章](https://dev.to/angelarae63/laptop-requirements-for-coding-3l0n)中推荐了它，现在我知道为什么了。我已经下载了 Visual Studio，准备好了！