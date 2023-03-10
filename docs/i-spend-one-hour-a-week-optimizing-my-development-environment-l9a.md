# 我每周花一个小时优化我的开发环境。

> 原文：<https://dev.to/oryanmoshe/i-spend-one-hour-a-week-optimizing-my-development-environment-l9a>

## 我不合理的效率困扰的故事——以及我用来助长它的工具

如果你像我一样是一个效率狂，你可能会发现自己处于以下情况(大概是在上周):

你有一个任务要完成，你坐下来，打开你的笔记本电脑，突然，它发生了。你每次都要做的烦人的小事。它可能一小时发生一次，一天一次，也许一周一次，但它会让你发疯。

它可以是一些小事，比如插入 MFA 代码、关闭弹出窗口，甚至切换到 vim 上的“正常”模式。这可能会更耗时，比如在重新加载 Mac 后重新排列显示器布局，将连接字符串更改为只读生产数据库，或者在更改不影响环境的情况下重新启动环境。

这并不重要，关键是这是你正在做的事情，而不是你想做的事情。

8 个月前，我决定停止疯狂。我开始每周预留 1 小时来摆脱这些小麻烦。

* * *

## 我们来谈谈工具

因此，有许多工具可以帮助我实现最终效率的目标。有些是免费的，有些要花一些钱，见鬼，我甚至花了 50 美元买了一个。你不必使用我建议的那些，对于每一个付费的，我相信你可以找到一个免费的或者自己创建一个。

### BetterTouchTool>*【7.5 美元-21 美元*

[BetterTouchTool](https://folivora.ai/) 是我用来定制我的开发体验的第一个工具，我甚至在开始我的当前职位之前就安装了它(我事先收到了我的笔记本电脑),我无法相信我每天会使用它这么多。
简而言之——它允许你定制 Mac 中所有“与输入相关”的东西，包括你的神奇鼠标、触控板、键盘，甚至你的触摸条(如果你使用兼容版本的话)

### >*自由开放源码*

Karabiner 是我希望每个操作系统都内置的工具之一。它允许你重新映射键盘上的任何键来做任何你想做的事情。为什么不是标准？？

### 阿尔弗雷德+动力包> *23 - 39*

Alfred 可能是我试图解决工作流程问题时的第一个工具。Alfred(一个免费工具，由一个付费的“Powerpack”进行了大规模扩展)有太多的功能要告诉你，但它本质上是对类固醇的关注。它允许无数的定制和功能开箱即用的改进，会让你希望你以前见过它。

* * *

## 改变我生活的变化

### 1。自定义我的 Touch Bar

如果你有一台带触控条的 Macbook pro，迟早你可能会对自己说:*“该死，这是一个未利用的潜力”*。
的确，苹果公司在这方面确实犯了错误。
很幸运，我用 BetterTouchTool 定制了它，让它不吸！

[![](img/4248fc7bfa0c5be37844fcaa4cf95b80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HenA-X3N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/anrt83c40mxjm1tu871y.png) 
[![](img/2a771f7c25feb86a7fae1d09af224c1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RdIdsoGk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrb0keutp3grlexesm4y.png) 
你可以看到我把`esc`键留在了左上角(我们稍后会谈到`esc`的情况)，我还放了一些我经常使用的应用程序的快捷方式(Finder、Hyper terminal、VS Code)

那个红色的`Production` /绿色的`Development`块表示我的`database.yml`文件的当前状态(在基于 rails 的应用程序中，你的连接字符串保存在那里)，这让我一眼就能知道我是在本地数据库还是只读生产数据库上工作。

我还添加了一个显示我当前内部 IP 地址的块(当我想与同事共享到本地服务器的链接时很有用)、一个静音按钮、一个带秒的日期时间块(当想了解查询性能时很重要)，它还可以带我点击我的日历，以及一个锁定我的笔记本电脑的“休息时间”按钮。

显然，当 Spotify 或 Youtube 播放时，我会在中间显示一些有用的功能。

我使用的另一个很酷的功能是用两个手指拖动来控制亮度，用三个手指拖动来控制音量。

### 2。用`ESC`替换我的大写锁定

如果你了解我，你可能知道我有轻微的键盘癖。我建造它们，我收集它们，我试着做最适合我风格的。

在我使用的每个系统上，我必须做的一件事是将我的 Caps Lock 键重新映射到`ESC`，尤其是在这个特定的 Mac 上。我的意思是，**到底谁会喜欢物理`esc`键？？**

如果你使用 vim，你就会知道让`esc`键尽可能靠近你有多重要，随着我越来越多地使用它，我意识到我在用它做 vim 之外的事情。关闭弹出窗口，模糊输入，在 Chrome devtools 中打开控制台。逃跑是钥匙的无名英雄。

我使用 Karabiner-Elements 将无用的 Caps Lock(现在是 2019 年，如果你使用 Caps Lock，是时候进行一些再教育)重新映射到一个`esc`。没有比这更简单的了。

### 3。太空学员轮班

如果你没有读过 Steve Losh 的这篇优秀的文章，我强烈推荐你去读，但是自从我读了它之后，我最喜欢(和使用)的定制是“太空学员移动括号”。

如果您的`left_shift`键被短暂按下，它会将您的`(`键转换为`(`键，将您的`right_shift`键转换为`)`键，当按住并与另一个键组合时，它会将它们视为正常的换档修饰键。

这使我可以一键输入括号，这比传统的`shift` + `9`方式有意义得多，因为我一天可能会使用数百次括号。

### 4。剪贴板历史

这是真正的游戏规则改变者。我特别使用了绑定到`cmd` + `shift` + `v`快捷方式的 Powerpack 中的 Alfred 剪贴板，但是你可以使用免费软件“Jumpcut”来代替。
这实际上是保存你复制项目的历史，包括文本*和*图像。

最初，我开始用它来停止这些*“F * CK，我复制了我剪贴板上的超级重要的东西，现在我不得不回去再复制一次”*我一直拥有的时刻，但随着时间的推移，我开始把它作为一个实际的工具来使用。

我从一个应用程序复制多个项目，然后转到另一个应用程序，并根据需要粘贴它们，而不必切换回来。

### 5。重新排列显示布局

如果你有一台 Mac 和两台显示器，你可能对这个问题很熟悉。你断开笔记本电脑的连接去参加一个会议，当你回来的时候，你的屏幕被弄得乱七八糟，空间被移动到他们想去的任何地方，你被留在那里拖着窗口从一个显示器到另一个显示器达 2 分钟。

我发现你可以改变你的主显示屏 3 次(如果你按照正确的顺序做的话)，显示屏就会回到你想要的位置！这本身就是一个巨大的时间节省，但我想让它更上一层楼，所以我创建了一个 Alfred 工作流。

工作流是一组动作，可以是脚本、设置更改、推送通知和大量其他动作，按顺序执行并由 Alfred 中的关键字或键盘快捷键触发。

使用[免费 cli 工具](https://github.com/jakehilborn/displayplacer) `displayplacer`，我能够在每次“主显示改变”后记录当前显示状态，在我将所有 3 个显示链接在一起后，我通过关键字 arr(arr 的缩写)触发工作流。

[![](img/aa8b048f464f28c07fa13f2d6e072ab1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4-ydKkzr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/89yqj7fhv6tgm5alvpu0.png)

现在每当我坐在办公桌前，我插上显示器，按下`⌥` + `space`(我的阿尔弗雷德快捷键)，输入`arr`，显示器就固定了！

### 6。更改我的数据库连接字符串

我们处理的数据量不允许我们将数据拉到我们的本地机器上并对其进行处理，因此我们有时必须更改我们的连接字符串(我们的`database.yml`文件)来连接到我们的生产数据库的只读副本，以查看数据在新仪表板上的外观。

这个过程每天大约发生 6-7 次，每次我都必须在我的编辑器中打开`database.yml`，取消对生产数据库行的注释/注释，保存文件并重启我的`rails server`(我们将在后面解决)

我创建了一个 Alfred 工作流，根据我输入的命令，**用正确的替换我的`database.yml`，然后**保存一个文件，说明哪个文件当前正在使用(还记得 Touch Bar 中的红/绿块吗？)

键入`dbd`将我的 DB 更改为**d**development，`dbp`更改为**p**production。
[![](img/854f359875c8fc3766bd1b1d8e3f71b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pYThqdYj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/384o6sx9lkibefxtxdob.png)

### 7。自动重启本地服务器

任何开发过 node.js 的人都知道`nodemon`，这是一个伟大的实用程序，允许您运行节点服务器，并在文件发生变化时重启它。
但是你知道吗`nodemon`可以在任何 cli 工具上工作，可以查看任何文件类型。

把我的`rs`别名(`rails server`)改成

```
alias rs="nodemon --watch config -e rb,yml --exec \"rails server -b 0.0.0.0 -p 3002\" 
```

允许我运行我的服务器，每当扩展名为`rb`或`yml`的`config`目录中的文件被更改时，它就会重启服务器！不再手动重启`database.yml`变化！

### 8。`sudo` TouchID

每当我需要输入我的 Mac 电脑的主密码时，我都很烦。为什么？因为我有 TouchID！为什么我应该键入密码，而不是通过生物识别来识别自己的身份？

我发现我可以在`sudo` PAM(可插拔认证模块)中添加一行简单的代码，从现在开始，每当我需要`sudo`一个命令时，我只需将手指放在传感器上！
只需在你的`/private/etc/pam.d/sudo`文件顶部添加下面一行:

```
auth sufficient pam_tid.so 
```

### 9。不带手机的 MFA

对于这一个，我会向你推荐这个启发了我的[奇妙的工作流程，但简而言之，你可以创建一个 Alfred 工作流程来粘贴 MFA 代码，而不必打开你的手机。](https://github.com/moul/alfred-workflow-gauth)

为了保证我的帐户安全(MFA 毕竟是有原因的)，我将脚本权限改为“仅 root”，并作为`sudo`运行。
如果你还记得第 8 点，在我的 Mac 上进行 sudoing 需要指纹，这允许我用指纹保护我的 MFA 令牌，而不必每次 GitHub 使我的会话无效时都伸手拿我的手机。

### 10。启动我的开发环境

我们的开发环境是半复杂的，我个人喜欢以特定的方式工作。
我的终端是超级的，完全按照我喜欢的方式定制，我有一个分成 3 个部分的标签，一个用于`docker-compose`，一个用于我们的`npm`运输工具，一个用于`rails server`。

除了那个选项卡，我还有 2 个选项卡，一个用于`rails console`管理，另一个用于`git`管理。

我没有在每次启动 devenv 时打开所有这些选项卡并键入所有这些命令，而是创建了一个由键盘快捷键`cmd` + `shift` + `b`触发的 Alfred 工作流，它为我完成了所有这些工作！
[![](img/be46423a7f113c27810eb70119cca018.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--R8T-wmnq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sp40zypz5h8udfgcgggi.png)

* * *

## 优秀奖

并不是我所有的优化背后都有一个故事，有些是微不足道的，但仍然可以节省大量的时间

### 别名

使用外壳别名。
每当你有一个很长的命令，你必须在一生中输入不止一次，把它扔进一个别名。你会惊讶于你使用它的次数。

### 终端配置

除了别名之外，在你的终端中还有很多定制工作要做。从外壳定制包括`fzf`和历史自动完成到更好的标签管理和更好的远程工具(如`mosh` )
终端是你的朋友，你花了很多时间在上面。善待它。

### 计算器

你可以把 Spotlight / Alfred 当计算器用。显然这并不像我想象的那么出名。
[![](img/0ecf4c84cf2b54119208291fea9bcd83.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--X0WkeoON--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i4652usmyomje32ov27o.png)

### 使用空格

我知道与 Windows windows 相比，这很难适应，但老实说，spaces 是绕过 Mac 的最佳方式。花点时间，学会使用它们，学会所有的手势。导航空间比拖动窗口要快得多。

### 触控板三指拖动

您可以用三个手指拖移，而不是在触控板上点按并拖移。打开这个手势，你的手指会感谢你！
你可以在首选项>辅助功能>鼠标&触控板>触控板选项中找到它...>启用拖动>三指拖动。

* * *

我有更多的优化，就像这里发布的一样，如果你想让我创建一个后续的帖子，只需说🙃

你花几个小时在电脑前，确保每一分钟都有价值。