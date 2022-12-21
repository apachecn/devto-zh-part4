# Chocolatey，Windows 包管理

> 原文：<https://dev.to/davidemily/chocolatey-package-management-for-windows-l66>

# 包管理器

作为一个长期的 Linux 用户，主流操作系统(MacOS 和 Windows)仍然困扰我的是缺乏可靠的包管理器。对于那些不确定我在说什么的人来说，软件包管理器是一个跟踪安装了哪些其他程序以及在哪里可以找到这些程序的版本的程序。因此，如果你在一台 Ubuntu 机器上，执行

`sudo apt-get update && apt-get upgrade`

将会找到所有当前被软件包管理器跟踪的程序，并将它们更新到最新版本。作为一个新的 Windows 用户，记住更新我的软件并记住它在哪里对我来说是一个持续的问题。幸运的是，我最近发现了 Chocolatey，它让 Windows 感觉更像家了。

#### 什么是巧克力？

Chocolatey 是一个用于 Windows 的开源软件包管理器。很像 Mac 版的家酿，Chocolatey 是一个令人愉快的命令行软件包管理器，已经为 Windows 发布。虽然它没有得到 Windows 的官方支持，但它在满足程序更新需求方面有着悠久的历史(自 [2011](https://chocolatey.org/docs/why) )。它需要 Windows 7+(Windows Server 2003)、PowerShell 2.0+和。NET Framework 4.0+才能运行。Chocolatey 确实有一个[付费服务](https://chocolatey.org/pricing)，其中包括非常酷的选项，如私人包源、包下载节流和许多其他资源。

使用它一直是一个梦想。目前，它确实有一个你可以下载的图形用户界面，但我还没有发现这种需要，因为使用它的命令行界面很容易和直观。使用这个神奇的工具最困难的部分是记住以管理角色运行 PowerShell，因为它缺乏切换到管理角色的能力

该软件也很容易安装，可以从主页[这里](https://chocolatey.org/)下载。虽然该网站包含了设置它所需的所有说明，但你只需要运行安装软件，在 Chocolatey 的[画廊](https://chocolatey.org/packages)上找到你想要的程序，然后按照该软件包的说明进行操作。

让您的软件包保持最新也很容易。通过运行

`choco upgrade all -y`

Chocolatey 将运行它管理的所有软件包，并从存储库中安装最新版本。虽然向 Chocolatey 添加现有程序很容易，但我最终只是在笔记本电脑上重新加载 Windows，这样我就可以向其中添加我所有常用的程序(并删除所有现有的 [Windows 膨胀软件](https://github.com/davidemily/DebloatWindows10))。

#### 替代品

像所有好的软件和流行歌星一样，有一些替代品试图模仿成功。具体到 Windows 的环境，这种替代方案是 [Ninite](https://ninite.com/) 和 Windows 10 自带的默认 Windows Store。Ninite 有一个非常直观的网站可以使用，并且有许多常见的安装。我对 Ninite 的唯一不满是，需要一个付费版本的软件来保持这些程序的更新。另一方面，自从我第一次尝试使用 Windows Store 以来，它就一直是个麻烦。GUI 过于繁忙，并且加载了我不想使用的软件的广告。

#### 结论

软件包管理人员让跟踪软件变得轻而易举。你再也不需要维护一个可以找到下载的. txt 文档，或者通过“关于”链接检查你的软件是否是最新版本。