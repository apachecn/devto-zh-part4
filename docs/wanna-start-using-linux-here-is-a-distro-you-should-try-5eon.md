# 想开始使用 Linux 吗？这里有一个发行版你应该试试。

> 原文：<https://dev.to/lazar/wanna-start-using-linux-here-is-a-distro-you-should-try-5eon>

当我很久以前把自己从 windows 中解放出来的时候，但我也很久以前意识到，人们通常只会一遍又一遍地向初学者推荐同一个 Linux 发行版:Ubuntu。

现在我并不反对使用 Ubuntu，但是我认为我们需要澄清是什么让一个发行版“对初学者友好”。

1.  易于安装的 GUI
2.  易于安装软件
3.  易用性

所以你注意到我经常提到软件。这是因为我发现这是驱使大多数用户离开的原因。

让我从 Ubuntu 和 Debian 的整体说起。

### 耳鼻喉科

### ☑️便于安装

安装是 Ubuntu 最简单的事情。只需输入你的名字，你希望你的用户名是什么，密码，以及你是否希望它完全安装或共享磁盘。简单。

### ***`X`*** 便于安装软件

Ubuntu 商店里有大量的软件，但是非常有限。你可以在网上找到很多软件，但是你必须自己去搜索。

## 问题:我们中有多少新手从网站上下载了一个安装程序，却发现它是为 Windows 安装的？

为 Ubuntu 找东西并不困难，只是也不容易。许多内容也必须作为存储库添加，通常涉及一个终端，这对 Windows 用户来说是一个噩梦。

此外，你几乎必须一直跑:

`apt-get update`

从命令行安装软件时。

### 易用性:

##### Ubuntu 极其好用！这就是为什么大多数人说它对初学者来说很棒。但是我要说的是，如果你不喜欢 Unity 桌面，或者不喜欢 Gnome，你必须从终端安装另一个桌面...这只是邀请新人离开。

这就是你在网上读到的一切我都要反驳的地方。

## 为什么作为初学者你应该尝试 Arch

当你在互联网上查找“Arch Linux”时，你听到的主要内容是:

“Arch Linux 不适合初学者”

#### 恕不同意！

### 易于安装

Arch Linux 因其硬安装而臭名昭著。具有讽刺意味的是，正因为如此，人们编写了许多 GUI 和半 Gui 安装程序，使得安装它变得轻而易举。以下是我推荐的几个:

#### Arco linux:

Arco Linux B 中有十几个桌面可供选择，安装程序非常简单。您也可以选择最小或常规。Min 只附带基本软件，regular 附带一堆。

#### Manjaro:

还有一堆桌面可供选择，安装程序也非常简单

#### 纳米布:

基本上和其他的一样，有一个非常用户友好的安装程序，有三个桌面可供选择

#### 无政府 Linux:

这是一个接近直上安装拱门，因为我会说，你作为一个初学者应该尝试。基本上每个桌面都有，你也可以选择你想下载的软件...但是！这是一个半图形用户界面，所以没有鼠鼠鼠在这里的人...仅键盘。

### 易于安装软件

有趣的是，我发现安装软件最技术化的方式也是最简单的。在 Arch 上安装软件有两种主要方式:

1.  图形 AUR 助手
2.  命令行 AUR 助手

我建议您使用命令行安装程序。

## 等等！

你刚才说的不就是会赶走新手吗？

是的，但是它自己平衡了。例如，如果你安装了 Manjaro 或 Arco 来查找软件，你所要做的就是:

```
yay PROGRAM-NAME 
```

其中 PROGRAM-NAME 是您要安装的程序的名称。

如果你没有安装这两个(初学者应该安装),你只需要输入`sudo pacman -S yay`就完成了。

你基本上可以做任何事情。

```
yay games

yay browser

yay whatsapp 
```

是的，你没看错，WhatsApp 在 AUR。基本上任何东西都在 AUR。那里有成千上万的应用程序。

这里是一个完整的 AUR 安装的例子。(看你能不能听懂我在这里开的小玩笑。)

```
[nico@niners ~]$ yay opera browser

14 aur/fifth-git 0.5+ggfcad9c2-2 (+0 0.00%) 
    Browser that carries the best features from Opera.
13 aur/opera-mobile-emulator 12.1-1 (+0 0.00%) (Orphaned) 
    Emulator of Opera Mobile browser. Discontinued.
12 aur/opera32 45.0.2552.898-1 (+2 0.02%) 
    A fast and secure web browser
11 aur/yandex-browser-ffmpeg-codecs-opera 0.0.2-1 (+2 0.01%) 
    symlink for opera-ffmpeg-codecs package to be used with yandex-browser
10 aur/fifth 0.5-1 (+7 0.00%) (Orphaned) 
    Browser that carries the best features from Opera
9 aur/certbot-git 0.31.0.r3.gec4c03fa6-1 (+26 0.00%) 
    A tool to automatically receive and install X.509 certificates to enable TLS on servers. The client will interoperate with the Let’s Encrypt CA which will be issuing browser-trusted certificates for free.
8 aur/otter-browser-git 0.9.99.r149.g6d9a7f72b-1 (+30 0.01%) 
    Browser aiming to recreate the best aspects of the classic Opera (12.x) UI using Qt5 - git checkout
7 aur/opera-beta 63.0.3368.22-1 (+33 0.00%) 
    A fast and secure web browser and Internet suite - beta stream
6 aur/opera-legacy 12.16.1860-4 (+34 0.00%) 
    Fast and secure web browser and Internet suite - legacy (pre blink) version
5 aur/opera-developer 64.0.3401.0-1 (+86 0.27%) 
    A fast and secure web browser and Internet suite - developer stream
4 community/otter-browser-nowebengine 1.0.01-2 (2.7 MiB 12.7 MiB) 
    Web browser aiming to recreate the best aspects of the classic Opera (12.x) UI using Qt5 without WebEngine support
3 community/otter-browser 1.0.01-2 (2.8 MiB 13.0 MiB) 
    Web browser aiming to recreate the best aspects of the classic Opera (12.x) UI using Qt5
2 community/opera 62.0.3331.66-1 (62.3 MiB 219.7 MiB) 
    A fast and secure web browser
1 community/certbot 0.36.0-1 (448.8 KiB 2.1 MiB) 
    A tool to automatically receive and install X.509 certificates to enable TLS on servers. The client will interoperate with the Let’s Encrypt CA which will be issuing browser-trusted certificates for free.
==> Packages to install (eg: 1 2 3, 1-3 or ^4)
==> 2

[sudo] password for nico: 

resolving dependencies...
looking for conflicting packages...

Package (1)      New Version     Net Change  Download Size

community/opera  62.0.3331.66-1  219.68 MiB      62.34 MiB

Total Download Size:    62.34 MiB
Total Installed Size:  219.68 MiB

:: Proceed with installation? [Y/n] y

:: Retrieving packages...
 opera-62.0.3331.66-...    62.3 MiB  1373K/s 00:46 [----------------------] 100%
(1/1) checking keys in keyring                     [----------------------] 100%
(1/1) checking package integrity                   [----------------------] 100%
(1/1) loading package files                        [----------------------] 100%
(1/1) checking for file conflicts                  [----------------------] 100%
:: Processing package changes...
(1/1) installing opera                             [----------------------] 100%
Optional dependencies for opera
    opera-ffmpeg-codecs: playback of proprietary video/audio
    pepper-flash: flash support
    upower: opera battery save [installed]
:: Running post-transaction hooks...
(1/4) Updating icon theme caches...
(2/4) Arming ConditionNeedsUpdate...
(3/4) Updating the desktop file MIME type cache...
(4/4) Updating the MIME type database...

[nico@niners ~]$ 
```

这里发生了什么？我输入了`yay opera browser`,这让它在 AUR 查找所有关于 opera 浏览器的参考资料。然后它会询问您想要安装哪一个。我输入我的号码，然后它开始了这个过程。Yay 然后告诉我需要多少磁盘空间，问我愿不愿意牺牲。之后，它要求我的密码，并为我安装。

也可以安装 Snap Store，就像 Ubuntu 的软件中心一样。

第二种方法是安装像 Octopi 或 Pamac 这样的东西，它是 Yay 的图形化替代。我确实发现 Yay 更可靠，但是这些也很棒。

1.  易用性这真的取决于你选择什么样的桌面环境。这就是为什么我建议你去看看 Arco 或者 Manjaro，如果你有兴趣的话，甚至可以去看看 Anarchy，因为你不仅仅局限于 Gnome 或者 Unity desktop。

好吧，我希望你喜欢这篇文章。我将为初学者做一些这样的比较文章，所以如果你想得到这些文章的更新，请跟我来。别忘了在下面评论你的观点！

## 祝你们今天过得愉快