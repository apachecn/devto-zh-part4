# 在 Windows 上像专业人员一样开发

> 原文：<https://dev.to/lietux/developing-like-a-pro-on-windows-4bbh>

所以在我看来，很多人似乎并不重视 Windows 的开发。似乎对 it 的能力、工具的可用性有很多误解，并且缺乏对真正选项的理解。

本指南希望能帮助你们认识到 Windows 和其他任何东西一样是一个可行的选择，而不仅仅是 C#。还有一些关于 Windows 使用的一般性提示，在开发中并不完全有用。

本指南基于我自己的工作习惯和原则，例如，如果你正在开发需要数据库的东西，我假设你无论如何都想避免在本地运行它，因为你会很快用各种服务毒害你的系统。相反，您通常应该尝试以某种方式在虚拟机或容器上运行这些东西。

在托管虚拟机或容器中运行更复杂的开发环境通常也是一个好主意，以确保重置环境或让新团队成员加入不需要精心制作的特定于操作系统的指南，该指南永远不会是最新的。

## 目录

*   [隐私等。对 Windows 10 的担忧](#privacy)
*   [安全](#security)
*   [终端仿真器](#terminal)
*   [包管理器](#packages)
*   [虚拟化](#virtualization)
*   [GNU 工具链、CygWin 和 WSL](#gnu)
*   [苹果制造](#apple)
*   [脚本、别名和自动运行](#scripting)
*   [路径](#paths)
*   [宋承宪键+选美](#ssh)
*   [窗口管理](#windows)
*   [截图](#screenshots)
*   [表情符号& UTF-8 特殊字符](#emoji)
*   [Fonts](#fonts)
*   [Git、Diff 和其他类似工具](#vcs)
*   自动重启怎么样？
*   [权限](#permissions)
*   [上述大部分自动化](#automation)
*   [结束语](#closing)

(嘿 dev.to peeps，介意让这些变得更容易吗？)

## 隐私等。对 Windows 10 的担忧

一开始就触及这个话题是公平的。

有些人似乎仍然有这些顾虑。我不能说运行任何大公司的操作系统是安全的，这些公司确实不值得任何人的信任，但看起来微软确实很好地加强和缓解了它们，并且有非官方工具进一步帮助它。

[![Windows 10 Offline account](img/ba6efc36ca64b9fcc3157840c9ae39f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--peCd0c5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3g9u8u8x51d7u7momrwn.png)

首先，当设置你的 Windows 电脑或用户帐户时，不要使用微软帐户，而要使用离线帐户。这将禁用一些大部分无用的功能，如同步 Edge 设置，你可能会被 Microsoft Store 唠叨，不时地将你的帐户切换到使用 Microsoft 帐户，但单击“否”并不需要太多努力。我很确定你也可以转换你的帐户，或者至少创建一个新的帐户，如果你一开始没有这么做的话。

第二，同样地，在安装你的电脑时，确保你对所有的追踪问题都回答“不”，对广告，对广泛的指标等等都回答“不”。-这确实使人们关心的许多事情无法进行。

[![DoNotSpy10](img/61046b372403facd716acfd3a0b10fcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8xrXuzl9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jqokqv6iv92ybiz3e19k.jpg)

第三，安装 [DoNotSpy10](https://pxc-coding.com/donotspy10/) 并调整你不喜欢的其余设置。它创造了奇迹。

在这之后，我会相信我的 Windows PC，至少和相信谷歌、苹果或 Canonical(在 Ubuntu 之后，以与亚马逊进行不正当交易而闻名)的任何东西一样。一些 Linux，BSD 等。发行版可能会更加注重隐私，但是我在其中投入的精力是有限的，我能容忍的系统崩溃程度也是有限的。

对于真正注重隐私的人来说，我可以强烈推荐 [Pi-Hole](https://pi-hole.net/) ，这需要做一些设置工作，并且作为一个“移动”解决方案并不容易，但是当在您的网络中配置时，您可以阻止许多您不希望在您的网络中出现的跟踪。看到它能从我的手机应用程序中阻止多少次追踪尝试，有点令人恼火。或者，我知道 [F-Secure FREEDOME VPN](https://www.f-secure.com/en/home/products/freedome) 也有一个阻止在线跟踪的选项，其他 VPN 解决方案也可以。

## 安全

安全性确实是一个同等适用于所有系统的话题。你可以在 MacOS 和 Linux 上感染病毒，也可以在网上做傻事，不管你用的是哪种操作系统。

在 Windows 上，默认情况下你是相当安全的，只要不去运行你的电子邮件附件中的每一个随机的`.exe`或者恶意网站想要下载的内容，你就没事了。

唯一的例外是全磁盘加密，因为我不知道任何其他解决方案比 BitLocker 更有效，并且需要 Windows 的 Pro 版本。有 DiskCryptor 和其他类似的东西，但在 Windows Update 破坏引导扇区后，我不得不花很大力气来修复它们。

[![BitLocker](img/9e39d5e14156d7e09a6e2cd3148d924f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gvHx_xk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpd5enuztc73ryqq0wgc.png)

如果你仍然担心，使用类似于 [F-Secure SAFE](https://www.f-secure.com/en/home/products/safe) 或许多其他安全套件中的任何一个，获得更高级别的保护。这种始终在线的主动安全工具在非 Windows 机器上并不普遍，所以我可以很容易地将它们归类为不太安全。

不要禁用用户帐户控制(权限对话框)，不要自动点击 UAC 对话框中的是，不要在你的 Office 文档中启用随机来源的脚本内容，你真的会很安全。

哦，还有一个额外的提示:使用密码管理器，不管你用的是什么操作系统。有很多选项，有些有更多的功能(例如跨平台同步，或与其他人共享等。)比别人多。选择任何一个并使用它，你将会比任何操作系统上的大多数人在互联网上得到更好的保护。

## 终端仿真器

因此，作为重要的第一步，让我们讨论终端模拟器。

没有人想查看或使用旧的命令提示符窗口(`conhost.exe`，而不是大多数人认为的`cmd.exe`)。

[![ConHost window](img/9b87762d324e5296ff5a664ea8f2c4db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rIecYF9p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9j401szlbdejy7l7r81r.png)

这个终端有很多问题，有点慢，没有跳转，复制粘贴很笨拙，字体支持有点差。但不要绝望！有选择。

1.  [科内姆](https://conemu.github.io/)

[![ConEmu window](img/0c2429434fc707fbe5e0672fc7d60d61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zlGYsF6h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8edgtsoyxd90emv3ccg4.png)

标签，分割视图，背景定制，主题，大量的定制选项，雷神之锤风格的下拉选项，配置文件，运行不同外壳的选项，等等。

是的，所有这些都可以通过 ConEmu 实现。如果你想要的是一个好的 Windows 终端模拟器，使用这个。很容易安装，不需要去别的地方找。

有一个缺点我觉得我需要提一下——它配置的默认提示有点烦人。你可能想要编辑`%PROGRAMFILES%\ConEmu\ConEmu\CmdInit.cmd`(查看[权限](#permissions)——下面的部分了解如何操作)或者调整应用内的设置来改变这一点。

1.  [新的 Windows 终端](https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701)

[![Windows Terminal](img/2c5a1e8615e3edc564a5b3e9d6ee16ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5JnYAS6O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dney938qkk96iztc97sx.jpg)

微软自己也意识到他们的终端有点糟糕，赶走了开发者。所以他们正在积极开发新的。它目前处于“早期预览版构建”阶段，需要 Windows 10 版本 18362.0 或更高版本。它看起来很有前途，但是可能有你不能使用它或者不想依赖它的原因——你可以使用 ConEmu。

1.  各种跨平台终端仿真器

现在我已经找到了几个，你有已经存在一段时间的基于 Node.js 的 [Hyper](https://hyper.is/) ，还有 [Bterm](http://bterm.bleenco.io/) ，就像[https://terminalsare.sexy/#terminal-emulation-applications](https://terminalsare.sexy/#terminal-emulation-applications)上的一个长长的列表——挑一个，挑 5 个，试试它们，看看你喜欢什么。

就我个人而言，我真的只喜欢其中的 [Alacritty](https://github.com/jwilm/alacritty) ，我尝试的其他程序感觉很慢或者很搞笑，例如，当你在运行默认安装之外什么都不做的时候，Hyper 往往会随机弹出一些关于`package.json`解析错误或其他类似的废话。

## 套餐经理

现在已经有一些了，例如 [Scoop](https://scoop.sh/) 和 [Chocolatey](https://chocolatey.org/) 等等。我不能面无表情地声称他们很优秀，例如，他们往往要求我启动一个单独的管理外壳来运行他们的命令，而不是在我运行它们时要求权限提升，但显然这个部门有希望，目前存在一些选项。

我个人已经用 Chocolatey 安装了一些东西，看起来没有问题。

## 虚拟化

如前所述，我依靠虚拟化来保持我的各种开发环境等。独立于我的主机操作系统，不管是什么操作系统。用各种项目特有的依赖关系毒害你的主操作系统似乎是错误的。很可能迟早会引发冲突和其他不可维护的情况。

由于我倾向于以一种与操作系统无关的方式工作，所以我所做的所有开发都需要使用跨平台工具。据我所知，目前只有两种基本技术可以做到这一点:

1.  [虚拟盒](https://www.virtualbox.org/)
2.  [码头工人](https://www.docker.com/)

如果你知道你将永远只使用 Docker，那么没问题，用他们推荐的方法安装 Docker，你就成功了。这取决于 Hyper-V，如果您还没有安装，安装程序会为您启用它，这应该会解决您的所有问题。我自己从来没有对 Hyper-V 满意过，所以你很有可能会遇到一些随机问题，比如游戏和其他东西中的反作弊系统，但对于纯开发使用来说，应该没问题。

对于任何其他情况，我强烈建议使用 VirtualBox，如果你也需要 Docker，要么通过 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) (托管 [Kubernetes](https://kubernetes.io/) 环境，通过`minikube docker-env`轻松访问 Docker)安装它，要么使用旧的&非常优秀的 [Docker Toolbox for Windows](https://github.com/docker/toolbox/releases) 。Docker 实际上是由一群脱离现实的人运行的，所以他们使得运行它的工作版本变得非常困难

我特意将 VMWare 作为一个选项进行了折扣，因为它不能很好地与您想要用来管理虚拟机的所有工具配合使用，例如[vagger](https://www.vagrantup.com/)，而且自从 VirtualBox 显著成熟以来，它对我来说似乎不值得花费这么多时间。

## GNU 工具链，CygWin 和 WSL

一些 GNU 工具(以及一些其他典型的*nix 工具)非常漂亮，虽然 Windows 也提供了一些类似的命令行工具，而 Power Shell 可能能够以一种简洁的方式完成一系列类似的功能，但我必须承认，我对这两种选择都不太满意。

我所做的是安装 [CygWin](https://www.cygwin.com/) (我使用`x86_64`版本)并把它放在我的`PATH`上，并使用 CygWin 安装工具来管理我可能要安装的各种东西。为什么要用`nslookup`，我可以用`dig`，`curl`和`grep`一样容易安装，`ls`也比`dir`好用。当一个工具捆绑了它们自己的`cygwin1.dll`或者你最终也安装了 MSYS2 时，会有一些偶然的问题，但是这些问题很少发生，并且很容易解决。我想 [MSYS2](https://www.msys2.org/) 也是 CygWin 的一个可行的替代方案，尽管我个人对它没有什么经验，它使用类似于 Arch Linux 的`pacman`来进行包管理，这很不错。

然后，当我遇到一些罕见的情况，我真的想使用的工具的 Windows 支持有点糟糕时，我往往能够在 Linux 的 Windows 子系统下运行得很好。举个例子，我在 Windows 上运行 [Jekyll](https://jekyllrb.com/) 时遇到了一些问题，因为他们的开发人员显然不关心让他们的工具跨平台，在 WSL 上运行就可以了。

有了 WSL，我可以在我的 Windows 上运行 X 服务器，例如 [VcXsrv](https://sourceforge.net/projects/vcxsrv/) ，甚至可以运行 GUI 实用程序。仍然有很多限制，例如，将 PulseAudio 路由到真正的 Windows 端似乎几乎是不可能的 atm，但这里也有相当多的承诺。

最重要的是，我有时真的很少在 VirtualBox 上启动 Fedora 来尝试一些甚至在 WSL 下也无法运行的软件。

## 苹果打造

需要 MacOS 来构建您的 iOS/MacOS 应用程序吗？你认为你需要为此购买苹果硬件吗？

有各种各样的构建自动化系统来处理它，还有像 [MacinCloud](https://www.macincloud.com/) 这样的东西来帮助处理它。

因此，这也不会阻止您使用 Windows，像这样的解决方案可能会引导您正确选择跨平台开发🙂

实际上，这种敌对的商业行为应该足以成为抵制苹果这样的公司的理由，但有时你必须面对现实，你也想为运行苹果硬件的人发布你的应用程序。

## 脚本、别名和自动运行

BASH 脚本对于某些事情来说是很简洁的，人们知道如何在它上面轻松地设置自己喜欢的别名。然而，这不是唯一的选择。

无论如何，Windows shell 支持许多基本操作，尤其是如果您已经安装了 CygWin/MSYS2。例如，我可以很好地运行`find . -name "*.sh" -exec git update-index --chmod=+x {} ;`或`cat README.md | grep https://`。环境变量？能做的，就用`set FOO=bar`。

[批处理脚本](https://en.wikibooks.org/wiki/Windows_Batch_Scripting)已经存在了很长一段时间，虽然对大多数人来说它看起来很陌生，但它通常足以解决您最常见的问题。

您可以编写`.bat`或`.cmd`脚本，也可以直接运行它们。例如，你可以运行`@FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i`，而不是像`$(minikube docker-env)`那样执行该命令的输出——如果你不习惯看的话，看起来肯定很奇怪，但效果一样好。对于批处理脚本功能范围之外的一些事情，也可以选择运行 VBScript，但是一如既往，我倾向于跨平台解决方案。

你可以在 Windows 上运行 BASH 脚本，通过 CygWin 安装`bash`(或者使用 WSL ),如果人们小心不要假设某些事情，它们会工作得很好。

对于任何稍微复杂一点的东西，选项往往是用 Python、Go 或 Rust 编写脚本或工具。Ruby 也是一个广泛使用的选项，有些人使用得相当成功，但对我来说，发布用 Ruby 制作的工具是一种痛苦。如果您正在进行 JavaScript 开发，您可以只使用`npm`脚本或各种 CLI 实用程序库。

那么别名呢？如果你想让你的`ls`真正运行`ls -haF --color=always`呢？嗯，有一个解决办法。

```
DOSKEY ls=ls -haF --color=always 
```

Enter fullscreen mode Exit fullscreen mode

如果你从没见过，这又有点奇怪，但它确实有效。

想永久保存吗？有办法通过 ConEmu 和其他一些工具来做到这一点，但对于一个更普遍的解决方案来说，这就是自动运行发挥作用的地方。

创建一个脚本，例如在`%USERPROFILE%\init.cmd`中，然后把你的东西放在那里。为了兼容和方便起见，在前面加上一行`@echo off`来禁止每次启动新的 shell 时输出命令。

这使得整个文件看起来更像这样:

```
@echo off
DOSKEY ls=ls -haF --color=always 
```

Enter fullscreen mode Exit fullscreen mode

现在你会想要打开`regedit.exe`并浏览到`HKEY_CURRENT_USER\Software\Microsoft\Command Processor`并用数据`%USERPROFILE%\init.cmd`(或者你的脚本所在的路径)创建一个新的“可扩展字符串”值，名为`Autorun`(如果还不存在的话)。

如果你相信你在互联网上找到的随机脚本的安全性(有没有注意到人们经常只是蜷缩着盲目地用管道攻击它？🤢)，您也可以将其保存为`autorun.reg`并双击它。

```
[HKEY_CURRENT_USER\Software\Microsoft\Command Processor]
"Autorun"=hex(2):25,00,55,00,53,00,45,00,52,00,50,00,52,00,4f,00,46,00,49,00,\
  4c,00,45,00,25,00,5c,00,69,00,6e,00,69,00,74,00,2e,00,63,00,6d,00,64,00,00,\
  00 
```

Enter fullscreen mode Exit fullscreen mode

这将为您提供一个脚本，您可以轻松地编辑并获取您的别名或其他常用命令，例如为您正在运行的每个`cmd.exe`实例设置 Docker 环境或任何您想要的东西，而不管它在哪个终端中。

## 路径

有些人可能没有意识到这一点，但是你也可以在 Windows 中管理`PATH`。你可以通过上面的脚本(`set PATH=%PATH%;<new item>` -注意作为分隔符的`;`而不是`:`)或者通过控制面板/开始菜单中的`View advanced system settings`选项来完成。在打开的`System Properties`对话框中，在`Advanced`选项卡上，点击`Environment Variables`，找到系统范围或用户特定的`Path`并点击`Edit...`。很简单，对吧？

[![View advanced system settings](img/f58c52a67d50c7bd1155ff03cc0e086b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RhMMx1OL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r271r8tkgn9ydybdurow.png)

把你的 CygWin `bin`目录放在这里。

## 宋承宪键+选美

根据我的经验，SSH 密钥在 Windows 上有点烦人，因为生成它们的唯一像样的工具似乎是来自 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) -suite 的 PuTTygen，但它默认使用自己的格式，你需要找到选项来从中获得适当的标准 OpenSSH 兼容内容。然而，这是非常可行的。

Pageant 是 Windows 的 SSH 关键代理。我还没有找到另一个真正有用的。

使用 PuTTy 安装程序获得 PuTTygen 和 Pageant。使用 PuTTygen 生成您的密钥，将`.ppk`文件存储在您的文件系统中的某个地方，如果您想让它们一直被加载，请将它们的快捷方式添加到您的`%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`-文件夹中，当您登录时，它们将被加载到 Pageant 中。

如果您这样做，请确保您的磁盘完全加密并具有合理的安全性，您不希望您的 SSH 密钥被轻易窃取。

## 窗口管理

对我来说，WinSplit Revolution 一直在做我需要的事情，快速有效地组织我的窗口，但它并不适合每个人，官方项目已经死亡。有一个分叉 [WinSplit Reloaded](https://github.com/pupitetris/winsplit-reloaded) 但似乎也没有积极开发。

虽然确实有很多选择，但是如果你需要它们，只需查看[https://www . slant . co/topics/1249/~ best-window-manager-for-windows # 1](https://www.slant.co/topics/1249/~best-window-managers-for-windows#1)获得一些其他选项的列表。

## 截图

我曾经运行[https://getgreenshot.org/](https://getgreenshot.org/)进行截图，我有点觉得我仍然应该这样做..但是`LWin+Shift+S`对我来说已经足够了，可以快速复制我屏幕的一部分并粘贴到我的通讯工具中，所以我很少需要其他东西。我也听说了很多关于 [LightShot](https://app.prntscr.com/en/) 的好消息，确实也有很多选择，这里再次列出一个选择:[https://alternativeto.net/software/greenshot/?平台=windows](https://alternativeto.net/software/greenshot/?platform=windows)

## 表情符号& UTF-8 特殊字符

如今`LWin+.`打开了一个表情符号选择器，当你想在你的输出、文档或其他任何东西中添加东西时，它非常漂亮。👍

不可否认，它在某些方面有点笨拙(我倾向于点击`Esc`来关闭它)，但在使用一段时间后，它感觉非常自然。

## Fonts

像所有其他系统一样，Windows 也支持字体，包括非常标准的 TrueType 字体。这些可以用来使你的 ide，以及你的终端模拟器更加清晰和好看。我个人非常喜欢在我的终端和 ide 上使用 [DejaVu 字体](https://dejavu-fonts.github.io/),因为它们看起来很干净，所有的字符都明显不同。

安装就像下载`.zip`，打开`Fonts`(在设置或控制面板中)一样简单。

[![Fonts in Windows Settings](img/ec4b4277211911a0f99c5c9b7a0e0357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v7OKiSV2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uhjyqw56jmwjcyfjv5ng.png)

看起来你也可以从微软商店买到一些字体，但是实际上那里提供的很少。

## Git、Diff 和其他类似的工具

忘掉人们似乎最终会遇到的可怕的事情。它用一些可怕的尝试来毒害你的系统，让 BASH 运行 Git，却不能让它很好地工作。

对于 CLI 的使用，你可以使用 https://git-scm.com/download/win[的官方二进制文件，或者通过 CygWin 重新安装。两者都应该可以工作，两者都需要一些配置来让你的 diff 工具完美地工作，也许还需要设置一个`GIT_SSH`环境变量来使用`plink.exe`(同样来自 PuTTY 包)来为你的 SSH 密钥使用 Pageant(【https://stackoverflow.com/a/10353937/3989287】](https://git-scm.com/download/win))来使用[。](https://stackoverflow.com/a/10353937/3989287)

但是，当您可以使用 GUI 工具来更好地理解存储库的状态以及您将要提交的更改时，为什么还要使用 CLI 呢？

我个人最喜欢的 atm 是 [GitKraken](https://www.gitkraken.com/git-client) ，但是 [SourceTree](https://www.sourcetreeapp.com/) 和[还有很多其他选择](https://git-scm.com/download/gui/windows)。

与众不同的是，我对 [WinMerge](http://winmerge.org/?lang=en) 情有独钟。我从来没有见过比它更好的区分/合并工具。[许多选项再次存在](https://merabheja.com/12-best-free-file-comparison-tools-for-windows-10/)。

## 自动重启呢？

是的，Windows 现在倾向于自动安装更新并在必要时重启。这是一种安全特性，如果您将编辑器设置为自动保存文件(尽管有些像 Sublime Text 这样的编辑器现在即使不保存也能记住内容)，并且不倾向于在终端模拟器或隐名窗口或类似程序中运行非常重要的内容，通常不会引起什么麻烦。

避免此类问题的最简单方法是在 Windows Update 设置下设置“活动时间”,这将阻止您在工作时间或类似时间进行更新。同样，如果你要去参加一个重要的会议，也许只需完全暂停更新-在同一个 Windows Update -settings 中有一个新的“暂停更新 7 天”按钮。

对我个人来说，这在我工作的一些电脑上也是不够的，因为我可能会让这个帖子打开很长一段时间，我不想回来时它已经消失了。

根据您的 Windows 版本，您还可以做许多其他事情。

*   [将自动安装改为通知](https://www.laptopmag.com/articles/stop-windows-automatic-reboots)
*   [取消升级服务器的重启能力](https://www.windowscentral.com/how-prevent-windows-10-rebooting-after-installing-updates)
*   [组策略更新和注册管理机构黑客攻击](https://www.makeuseof.com/tag/disable-forced-restarts-windows-update/)

根据您的需求和情况，其中一些比其他的更适用。

## 权限

> 但是讨厌的文件权限怎么办？我不能在需要的时候编辑文件！？！？

人们根本不了解 Windows 上的文件权限。人们禁用用户帐户控制，或者只是安装`Program Files`之外的东西或类似的东西来绕过他们不理解的权限系统，而不是学习它。

这就像建议你以`root`的身份运行所有东西，或者在 Linux 上运行`chmod -R 0777 /`，或者禁用 SELinux——太疯狂了，但是你 100%知道有人这么做了，因为他们懒得去学。

如果你需要运行一个命令，而你没有权限做这件事——以管理员的身份运行命令提示符/其他应用程序，这很容易。在“开始”菜单中，你通常可以右键单击任何项目，然后选择“以管理员身份运行”。把这个想成`sudo`。

那么，当您觉得您的用户应该能够访问这些文件，而您却不能时，该怎么办呢？您可以编辑特定文件或文件夹的权限。

打开文件浏览器，浏览到有问题的内容，右键单击条目并选择“属性”。

[![Windows File Properties](img/67b1179a9f3e816555de06b71f1ad2d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rDvPWyhJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/plctcqat96vg759opo0t.png)

然后切换到“安全”选项卡，您可以查看为其设置的权限。想要更改它们，例如为自己添加权限？

点击`Edit...`，然后例如`Add...`，当它要求“输入要选择的对象名称”时，你只需输入你的用户名，在我这里是`Lietu`，然后点击`OK`。

在屏幕上选中你自己后，你可以点击`Full control`的`Allow`复选框，然后点击对话框上的`OK`，瞧，你的问题就解决了。

[![Windows File Security](img/8d9c6c85db53f6f67c38470c7bce72a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SiHVarja--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qn2x46qh2s6htiwumal.png)

这更类似于`sudo chown <username> file`。

## 自动化程度高以上

我写了一个工具来自动化这些事情，[Aperture Control](https://github.com/Lieturd/aperture-control)-free(BSD 3-clause)工具和 PowerShell 脚本、批处理脚本和注册表补丁的集合，例如，从 Chocolatey 和 Scoop 自动安装各种工具，或者根据您的喜好配置您的 Windows 设置。

在 https://github.com/Lieturd/aperture-control-recipes 有一系列现成的食谱，希望这些食谱能继续增长。

使用 Aperture Control，我还编写了一个模板，我认为它是一个很好的标准开发环境和开始使用 Aperture Control 的良好基础。希望它也能帮到你。这有点自以为是，但我试图将大多数个性化调整排除在外，只添加我认为非常普遍需要的东西或使 Windows 开发更好。

## 关闭思绪

这些年来，我一直是一个严重的*nix 用户，但实际上我发现不可能找到一个让我感到舒服的*nix 桌面。

Windows 过去要糟糕得多，如果你有不好的经历，我可以理解，但自从 Windows 7 问世以来，微软在稳定性和可靠性方面确实有了显著改善。你可能遇到的错误越来越容易在网上找到解决方案，而且一般来说你遇到的错误越来越少。我发现 Twitter 上的[@ MicrosoftHelps](https://twitter.com/MicrosoftHelps)确实是我拥有的最好的客户支持体验。

Linux 有太多的驱动程序问题、错误和设计不良的功能，例如，拖放不工作或者有单独的剪贴板用于鼠标选择和`Ctrl+C` & `Ctrl+V`。

MacOS 太蠢了，甚至没有基本的能力来控制每个应用程序的音量，或者调整任何设置。如果你不喜欢苹果喜欢的方式，那太糟糕了。

真的没有其他足够成熟的桌面环境拥有我所依赖的工具。

多年来，Windows 一直是一个很好的开发环境，并且随着微软积极地修正问题，增加 WSL 之类的东西，并真正听取来自开发者的反馈，windows 变得越来越好。

最重要的是，你可以得到一台更强大的工作电脑，内置 4G 调制解调器、触摸屏等。只需一台苹果电脑的零头。

我想听听你们有没有其他的建议，或者你们可能想提到的关于 Windows 开发环境的问题——我们也许可以一起解决一些仍然存在的问题。

我的下一个目标很可能是尝试自动化我上面写的一堆东西——通过运行一个批处理脚本或 VBScript 文件为自己建立一个漂亮的 Windows 环境。如果你对此有任何建议，我洗耳恭听🙂

# 资金支持

多亏了联合创作者和[列图](https://lietu.net)，这项工作才成为可能。你可以通过在[给我买杯咖啡](https://www.buymeacoffee.com/cocreators)上支持我们来帮助我们继续工作。

[!["Buy Me A Coffee"](img/8741c1bafaddaa2b9ee9d5b1c31c3e03.png)](https://www.buymeacoffee.com/cocreators)