# 我最喜欢的 Linux 工具

> 原文：<https://dev.to/antjanus/my-favorite-linux-tools-12be>

> 为了存档，这篇文章被交叉发布到我的个人博客上。

我想列出几个实用程序，它们或者有助于我在 Linux 上的工作效率，或者以某种方式使在 Linux 上工作更愉快。

我真的很喜欢设置工具，这样当我使用时，它们更容易使用*——所以不要只是说“这个工具很棒”，还值得注意的是，一旦你花时间设置它们，一些工具会非常强大，即使你不经常使用它们。*

您可能会注意到，其中大多数都是基于终端的实用程序。我主要通过终端与 Linux 交互，几乎所有不基于终端的东西都是常见的跨平台应用程序(Discord、Opera、Slack、VSCode 等)。)所以这些我都不提。

> 我[向社区](https://dev.to/antjanus/what-are-your-favorite-linux-utility-productivity-tools-57al)征求大家的推荐。

## [NeoVIM](https://neovim.io)

[![neovim text editor screenshot](img/ff8415450a88b8b35af1d7d665938b2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OOWj3yqJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/nvim-screen.png)

从 git 消息到快速文件更改(或完整开发)，设置 NeoVIM 至少语法高亮是完全值得的。当我不想打开 VSCode 或者 VSCode 对于快速编辑来说太多的时候，我经常使用 VIM。

为什么是 NeoVIM 而不是 VIM？NeoVIM 保留了与 VIM 的完全向后兼容性，并公开了一些新特性。哪个都行！:)

## [ZSH](https://www.zsh.org)

[![zsh shell screenshot](img/7ea42b24ba617b1814cf603e4086803e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3G5e4OwM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/zsh.png)

我已经按照我喜欢的方式设置了我的 bash 配置，但是最近切换到了 ZSH。为什么？

1.  设置起来既快又容易
2.  你不必是一个超级用户来利用它
3.  zsh 命令高亮显示使内容更容易阅读
4.  主题支持比 bash/terminal 主题支持好得多
5.  许多插件

请务必查看 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) ，它为您提供了主题支持和插件支持。

## [nmtui](https://developer.gnome.org/NetworkManager/stable/nmtui.html)

[![nmtui network manager screenshot](img/2b148374b684eaf2865a44df48259566.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3yIiHQNm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/nmtui.png)

用于网络管理的 TUI(终端用户界面)。我比其他任何内置的工具都更喜欢它，因为它非常简单明了。出于某种原因，我也更信任它。当 GUI 网络管理器死机时，我总是怀疑 GUI 死机了——而不是网络管理器在做它的工作。有了 NMTUI，我肯定会更加信任。

我知道，我知道，它看起来像是 80 年代的产品，但它允许您管理所有保存的 wifi 设置、以太网连接等等。据我所知，它功能齐全，什么都不缺。

## [ncdu](https://dev.yorhel.nl/ncdu)

[![ncdu disk usage utility screenshot](img/959510cb99e5b2d6ef29228e60b7871c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4P9EZZKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/ncdu.png)

NCDU 是`du`命令的终端 UI(更具体地说是一个`ncurses` UI)。`du`是做什么的？杜是个`disk usage`公用事业。`ncdu`的酷之处在于它扫描文件夹或系统中的所有东西，并允许你用所有计算出的大小值探索你的文件系统。

这是*所以*当你试图找出是什么占用了你的驱动器上这么多的空间，或当你试图删除空间猪有用。

## 

[![htop process viewer screenshot](img/6ea8bef84c81bae7c294d36037a6cfff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ot8RNSdb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/htop.png)

另一个终端实用程序。这是一个交互式流程查看器。把它想象成你的活动管理器或任务管理器。它可以通过流程进行过滤、搜索和排序。它显示所有相关的进程信息，包括启动进程的完整命令路径。这在试图寻找失控节点进程时非常有用。

然后，您可以向进程发送命令，但是我真正使用的唯一一个命令是`kill`命令来关闭阻塞的进程。

## [通知-发送](http://manpages.ubuntu.com/manpages/xenial/man1/notify-send.1.html)

[![notify-send terminal and notification example screenshot](img/3b50b1551b1c311af6b68bf9c2cf02ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aEJFyWJf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/lmk.png)

`notify-send`是一个实用程序，允许您向自己发送通知。这非常有用——以至于我在我的`.bashrc` :
中使用了别名

```
alias lmk="notify-send 'Something happened!'" 
```

Enter fullscreen mode Exit fullscreen mode

可能看起来没什么用，但是如果你正在运行一个长时间运行的过程，有一个通知系统让你知道它什么时候完成是非常好的。所以我倾向于将`notify-send`与其他命令结合使用，比如`npm install && lmk`。

## [罗菲](https://github.com/davatorium/rofi)

[![rofi application launcher screenshot](img/e4e5acbc672aea4adef7b6e31a8a27e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QjHXxEbH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://antjanus.com/wp-content/uploads/2019/06/rofi-1.png)

不像其他的，这个不是一个终端应用。这是一个应用程序启动器/窗口切换器/等等。如果你使用 i3 这样的东西，它是 dmenu 的完美替代品。如果你在用别的东西，它仍然是一个电动工具。

它的特别之处在于，它可以同时处理命令行实用程序和 GUI 应用程序，还可以将你切换到一个正在运行的应用程序实例。最重要的是，它快得惊人。

## 其他值得注意的工具

我不想对我使用的每一个工具都穷追不舍，所以这里有几个我要检查的:

1.  [bat](https://github.com/sharkdp/bat)——对`cat`的替代，具有语法高亮和更好的文件内容显示。安装并将其命名为`cat`
2.  notepadqq -灵感来自 Windows 专用的 Notepad++，一个易于使用的自动保存草稿的文本编辑器
3.  emacs org-mode 是一个强大的文本编辑器，org-mode 是一个纯文本的生产力/任务管理工具
4.  一个桌面系统监控工具告诉你 CPU/RAM 负载，活动进程等。在您的桌面上
5.  Archey -一个很酷的工具，可以告诉你的机器。它被用来生成这篇文章顶部的截图

> **注意:**如果你使用我的截图做任何事情，请让我知道并以某种方式信任我。尤其是当它被用作 AAA 视频游戏中[电脑黑客监视器的纹理时](https://twitter.com/AntJanus/status/800825075880448000)
> 
> 喜欢我的内容？[在推特上关注我](https://twitter.com)或者只是[请我喝杯咖啡](https://www.buymeacoffee.com/CKdpP4G)