# 如何在新的 mac minis 上启动和使用外置硬盘

> 原文：<https://dev.to/superdiana/how-to-boot-use-an-external-hard-drive-on-new-mac-minis-18d8>

## [T1】简介💅](#intro)

我“抢救”了一台 2018 款 mac mini(第八代 i3，8Gb Ram，128SSD)。我的想法是将其用于我的个人家庭录音室。我知道 ram 是可以升级的，但是存储却被焊接到了单元里。iFixit 给新款 Mac mini 的可修复性评分为 6/10，10 是最容易修复的，超过了最新的 MacBook Air、MacBook、MacBook Pro、iMac 和 iMac Pro，仅落后于 2013 款 Mac Pro。点击阅读原文[。](https://www.macrumors.com/2018/11/09/ifixit-mac-mini-2018-teardown/)

> *“新款 Mac mini 获得了更高的可修复性得分，这要归功于它的拆卸简单，没有强韧的粘合剂或专有的五瓣螺丝以及用户可升级的 RAM。然而，由于焊接的 CPU、存储和端口，它没有获得满分，影响了维修和升级。”*

* * *

因此，找到一个外部 SSD 并从那里运行操作是有意义的。只是 logic pro x 需要 70 Gb 的所有样本等。当我让所有东西都以最低速度运行时，我只剩下 15 Gb 了。所以我是这样做的:

## 算出来的🤹‍♀️

### 你会需要🧐什么

*   外置 SSD 硬盘。(我不会建议品牌或容量，留给你觉得适合自己的吧)
*   苹果电脑。

### 完成工作🤓

*   首先，去磁盘工具，格式化你的新硬盘。为此你必须选择`APFS`或`MacOs Extended (Journaled)`，加密由你决定。只要确保你记得你的密码。(⚠️这意味着你的整个外部硬盘内容将被擦除！小心操作，为安全起见，请使用新的外置硬盘)
*   一旦你格式化了你的硬盘，重启你的 Mac，然后在你看到苹果标志后立即按住`Command` (⌘) `+R`。您的 Mac 从`macOS Recovery`启动。
*   当您看到 macOS 实用程序窗口时，选择`Utilities > Startup Security Utility from the menu bar`。
*   当要求您鉴定时，点按`Enter macOS Password`，然后选取一个管理员帐户并输入其密码。
*   您将看到类似这样的内容:

[![Startup Security Utility](img/595c7291ae005ba265a8c282321d6d5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XC-5jim4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://support.apple.com/library/content/dam/edam/applecaimg/en_US/imac/macos-high-sierra-startup-security-utility.png)

*   选择`Allow booting from external media`

我们差不多完成了。暂时不要重启任何东西。返回第一个屏幕，点击`reinstall macOS`:

[![macOS Utilities](img/d1058ebe6153d136035c935ad376820c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jPndI-6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://support.apple.com/library/content/dam/edam/applecaimg/en_US/macos/highsierra/macos-high-sierra-recovery-mode-reinstall.jpg)

*   接受所有条款和条件，系统会提示您选择要安装操作系统的硬盘。点击它并安装。机器将重启几次。
*   你最终会回到你的常规登录界面。如果您所在的机器将引导您的外置硬盘，您有两个引导选项。您可以在每次打开电脑时按下`option`键，选择从哪个硬盘启动，或者您可以永久切换到外置硬盘。

如果您希望机器总是从外部硬盘启动:

[![startup disk](img/6bc03efc95effec549190f9e672a843d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QqgXgfmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://support.apple.com/library/content/dam/edam/applecaimg/en_US/macos/macos-sierra-system-startup-select.png)

*   选择`Apple` () `menu` > `System Preferences`，然后点击`Startup Disk`。
*   点按锁图标并输入您的管理员密码。
*   选择启动磁盘，然后重新启动 Mac。

## 那都是乡亲们！🐷

享受您所有的额外储物空间！。请记住，您可以随时切换回原来的启动盘！

再见。

### 2020 年 11 月 20 日:

[@ darkcisium](https://twitter.com/darkcisum)在推特上补充了以下信息:

`Hi. I've been following your guide on booting a Mac Mini from an external SSD.
Just thought, maybe it could be helpful for others, in the Disk Utility one needs to "View > Show All Devices" in order to be able to select the disk itself`

非常感谢！！