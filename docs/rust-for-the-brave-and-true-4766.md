# 为勇敢和真实而生锈

> 原文：<https://dev.to/gruberb/rust-for-the-brave-and-true-4766>

[![thinkpad and linux](img/0f752f0cdcc2103e7d37d8833a0f3ae9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bTJvQ6Vb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jtwjm41l472ciamvqzi6.jpg)

几年前，我想从前端切换到后端。有一天，我掉进了 YouTube 的兔子洞，并(部分地)观看了一些技术讲座。我意识到许多真正好的会谈都是在 Clojure 会议上进行的。我研究了一下，决定学习 Clojure。

学习一门新的编程语言的渴望往往来自于对旧工作的纯粹厌倦，所以你也想重做自己的一部分。经过一番研究，我找到了一本名为[《clo jure for the Brave and True》](https://www.braveclojure.com/)的书，绝对喜欢。

然而几个星期后，我放弃了。这本书希望你全身心投入并同时学习 emacs，因为根据这本书，它提供了最好的环境。我真的很喜欢这个想法，但也因为我所在地区(德国柏林)缺少初级职位而放弃了这个项目。

# 生锈

几年后的今天，我开始学习 Rust，并对整个生态系统、社区和可能性感到兴奋。在过去的几个月里，我在 Rust 中阅读、写作和编程了很多东西。以至于我从 8 月份开始在一家公司得到了一个生锈的职位(耶！).

# 勇敢

但是，随着我越来越深入地研究这种语言，并观看教程，我发现:我无法在运行 macOS 的 MacBook Pro 上进行系统编程。不要误解我的意思:一旦你掌握了一项技能，你在哪里操作它并不重要。另外，不同的机器不会产生很大的不同。

虽然，我可能有点不同意。以减肥为例。仅仅每天减少几千卡热量不会有很大的不同。你的冰箱必须看起来完全不同，这样你就不太想吃所有不好的东西，自然会以更健康的方式滋养你的身体。

> 我想起了“Clojure for the Brave an True”的概念，并想把它应用到我的 Rust 环境中

这正是我订购 Thinkpad X1 时的目标，我打算在上面安装 Arch Linux。我不想滋养我的身体，而是想用正确的方式滋养我的大脑。例如，如果我想学习 TCP/IP，我不想时不时地学习正确的软件包。

### 我的理由:

*   我想把几乎所有的时间都花在命令行上
*   我意识到，即使是 macOS 也是休闲和浪费时间的完美来源:我想要一台纯粹的工作机器
*   我的计划是不在屏幕前度过空闲时间，而是看书或写作
*   我想减少操作系统的干扰
*   我想感觉更接近机器

# 真实

[![vim and Rust on Arch on a Thinkpad X1](img/79a1927289a1e226e67d1ad5ee456d7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0doHD3ww--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gruberb/dotfiles/master/code.png)

在收到我的 Thinkpad 后，我移除了 Windows 并从 u 盘安装了 Arch，[按照这些步骤](https://github.com/gruberb/dotfiles)。经过 4 次尝试，我终于成功了。我第一次开机的时候安装了 [`i3`](https://wiki.archlinux.org/index.php/I3) 和 [`alacritty`](https://github.com/jwilm/alacritty) 作为我的终端。在这旁边，我安装了`Firefox`用于浏览。就是这样。我需要一个终端，一个窗口管理器和一个浏览器。我几乎不再碰我的鼠标了。

### 我从中学到了什么

*   使用我的浏览器来跟上 Slack 和电子邮件，我终于达到了我的目标，每天只查看两次(因为它只是不太方便)。
*   仅仅是安装 Arch 就让我接触了一个操作系统的很多方面(又一次)，理解某些领域并不困难
*   我可以买像《Linux 编程接口》或《T2 TCP/IP 指南》这样的书来深入研究这个系统
*   我可以阅读 2600 杂志和教程
*   这让我在工作时更少分心，因为我实际上没有通知或其他分心的事情

在 macOS 下工作，我不断地打开其他程序，而我不知道如何进行一项任务。有了 Arch，这样做要困难得多(也没那么有趣)。我旁边有一个实体笔记本，如果我不能继续处理一个问题，我会在笔记本上记下我的想法，然后盯着窗外再次思考。

我再次兴奋地接近我操作的硬件。能够掌握`nvim`并设置适当的`tmux`设置比在可视代码或其他编辑器中做同样的工作流程要快得多。

# 铁锈编码环境

我正在使用:

*   [NeoVIM](https://neovim.io/)
*   [tmux](https://github.com/tmux/tmux)
*   [信任语言服务器](https://github.com/rust-lang/rls)

使用以下 NeoVim 插件:

*   [neovim-airline](https://aur.archlinux.org/packages/neovim-airline/)
*   [LanguageClient NeoVim](https://github.com/autozimu/LanguageClient-neovim)
*   [取消自动完成](https://github.com/Shougo/deoplete.nvim)

就是这样。你可以很容易地设置一个`tmux`会话，将你的代码所在的窗口分割成`nvim`和另一半的窗口`cargo run/watch`。

像这样浏览代码，过一段时间后就很难再使用普通的代码编辑器了。尽管你可以安装绑定到 VS 代码，我猜。

# 2 个警告/提示

*   随身携带一个带有 Arch 的可工作的实时 u 盘，以备不时之需。我修改了启动脚本，让自己陷入了一个循环。我不得不从硬盘启动，解密并安装我的硬盘，然后恢复更改，以便能够再次启动。

*   除了我的 Thinkpad，我还操作一台远程服务器(每月大约 10 欧元)进行备份(通过 [`rsync`](https://wiki.archlinux.org/index.php/Rsync) )和其他有趣的活动。万一您想要对最终完美配置的 Arch 安装进行快照，拥有另一台机器进行备份会有所帮助。

# 添加内容

除了 Thinkpad，我还得到了一个[垂直鼠标](https://www.amazon.com/Vertical-Mouse-Ergonomic-Optical-Wireless/dp/B07HSMS6Y2/ref=sr_1_11_sspa?keywords=vertical+mouse+csl&qid=1560258082&s=gateway&sr=8-11-spons&psc=1)和一个[合适的键盘](https://mechanicalkeyboards.com/shop/index.php?l=product_detail&p=4322)。这两者甚至增加了我的设置的乐趣。我的显示器是戴尔 4k 27 英寸，连接在显示器支架上。

这是如此美好的工作，我甚至不想念美丽的 5k LG 超细，我以前有。

# 总结

我在 GitHub 帐户上为我的设置提供了基本的点文件和步骤。和某些环境背道而驰是好的。例如，iPad 变得如此强大，以至于普通用户不再需要笔记本来完成日常任务。这是重新考虑你与你正在使用的工具之间关系的绝佳时机。

如果你按照步骤操作，在 Thinkpad 上操作 Arch 真的很简单。您需要到处修补 HDPI，并调整一些 hibernate 功能。但总的来说，这是我想要的:一个舒适的编码环境的最低要求，几乎没有干扰。

最后，Thinkpad 的键盘也比 MacBook Pro 好得多。此外，外形也非常完美。比 13 英寸稍大，比 15 英寸稍小。