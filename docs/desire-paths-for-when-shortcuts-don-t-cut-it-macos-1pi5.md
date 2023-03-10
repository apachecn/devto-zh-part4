# 欲望之路:当捷径不可行时

> 原文：<https://dev.to/effectivedev/desire-paths-for-when-shortcuts-don-t-cut-it-macos-1pi5>

> 总结:你不需要昂贵的键盘来提高便利性。完全的可定制性是非常好的，但是我们可以通过简单的软件实现一半。您可以使用“系统偏好设置”和软件工具来自定您的键盘，以使您的导航更加舒适，并有助于防止错误。

[![Desire Paths: For When Shortcuts Don’t Cut It [macOS]](img/02b0d5e7ed3122ea13748f763b905de3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k5zrWV2c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1560330872-a94111c8dbcf%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

无论你是整天坐在电脑前，还是只想快速完成某项工作，你可能已经意识到在键盘和鼠标之间切换需要一段时间。加在一起，这些时刻可以吃掉一大块时间。这种浪费的时间对于生产力来说是非常糟糕的，如果你按下了键盘并且错过了首页，情况会变得更糟。

这种延迟是大多数现代操作系统的一个已知限制，因此，有一个解决方案:键盘快捷键。键盘快捷键也存在问题。它们中的大多数都是为解决一般问题而设计的，没有经过优化。如果你打过`Command + Tab, Tab, Tab, Tab, Tab`，你就知道我的意思了。

记住键盘快捷键可能是一项艰巨的任务。像大多数事情一样，记住它们的关键是使用它们。像复制和粘贴这样的通用方法通常很容易记住。为你使用的每个应用程序创建自定义的备忘单也很方便。然后还有定制它们的选项。

## 什么时候键盘快捷键不是快捷键？

这种情况在你身上发生过吗？你在应用程序之间切换并关闭它们。你以为你开着 iTunes(我手机连接了吗？！)所以你去用`Command + Q`关闭它，好看又简单。只不过你刚刚关闭了一个打开了 5 个以上标签的窗口。哎呀。

突然间，这条“捷径”不再是捷径了。幸运的是，有一种方法可以针对特定的应用程序禁用此功能。如果你进入系统偏好设置，点击键盘，你会看到一个快捷键标签。如果您为“退出[应用程序名称]”菜单项创建快捷方式，则该菜单项的任何现有快捷方式都不会被触发。请注意，您需要确保它与菜单项匹配，而不是与应用程序的名称匹配。比如你打开 iTerm，显示的是“关闭 iTerm2”，而不是“关闭 iTerm”。

<figure>[![Desire Paths: For When Shortcuts Don’t Cut It [macOS]](img/84dfa1b089c4106f3ce20813a9a6f44a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SLYXQVIn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://effectivedev.com/conteimg/2019/08/Messages-Image-507522627-.png) 

<figcaption>我有限但格外方便的省钱妙招。</figcaption>

</figure>

在我的例子中，我将这个添加到 Safari 和 iTerm 中。我可以坐在我的 iTerm 窗口中，按下`Command + Q`或`Command + W`，但什么也没有发生。

## 什么时候键盘不是*只是*一个键盘？

几年前，当我在一个联络中心工作时，我决定购买一个机械键盘。机械键盘很快成为一种爱好，大约五个键盘之后，我找到了我的终极目标，OLKB 的漂亮的 40% [普朗克键盘。什么时候键盘不仅仅是键盘了？当它是一件艺术品时。](https://effectivedev.com/keyboard-hacks/)

Planck 的一个主要好处是它运行在 QMK 固件上，这提供了无限的可定制性，包括改变键映射。我写了一篇关于它的[文章](https://effectivedev.com/keyboard-hacks/)，包括我的键图。普朗克是一个神奇的键盘，我从它那里学到了一些我最喜欢的“欲望之路”。

我唯一的问题是我需要从我的办公桌走到会议室。我经常发现，在普朗克键盘和内置的 MacBook 键盘之间切换让我的手感到困惑。然而，我的 MacBook 键盘没有办法添加这一功能。至少我是这么认为的。

## 什么时候一把钥匙*比一把钥匙少*？

如果你像我一样是个终端瘾君子，你可能会经常使用控制键。如前所述，键盘快捷键是避免切换到鼠标的好方法，但是在简化的、通常没有鼠标的终端环境中，它们变得更加重要。

我可以写一篇关于 Bash/Zsh 的快捷方式的文章。(这主意不错。期待未来。)大多数 bash 快捷键，以及许多用于终端程序的快捷键，如 [tmux](https://effectivedev.com/keyboard-hacks/) 和 [Vim](https://effectivedev.com/keyboard-hacks/) 都使用控制键。值得注意的例外是 Vim 进入“正常模式”的 Escape 键。

> 作为旁注，Vim 的“进入正常模式”快捷方式一般是`Escape`，但也存在运行速度更快的`Control + [`。

那么，如果控制键如此重要，它又是如何做到的呢？如果你认为你知道为什么，请在下面添加评论。除了糟糕的位置，还有一个解决方案。一语双关，但你马上就会明白。在系统偏好设置的深处，苹果确实让你调整这个。

您可能已经注意到，在使用基于终端的应用程序时， [Home Row](https://effectivedev.com/keyboard-hacks/) 经常出现。主排是你的手应该休息的位置。能够将最关键的键放在这一行很有帮助，这也是 Vim 如此喜爱它的原因。Home 行包含一个对终端使用最有用的键:Return 键，以及一个最没用的键:Caps Lock 键。

在这个时代，大写锁定键毫无用处，除非你在 YouTube 评论中找到自己。这可能是为数不多的几个被意外启动而非有意启动的按键之一。我相信你可以看到这两个领域的冲突。让我们把黄金房地产的大盘股转换成真正有用的东西。

在“系统偏好设置”中，打开键盘面板并点击“修饰键…”打开的模式给了我们一些可定制性。我们可以选择将“Caps Lock”选项设置为“Control”值。这里有一个 gif，因为现在已经不是 2005 年了，静态图像不能处理它。

[![Desire Paths: For When Shortcuts Don’t Cut It [macOS]](img/1bf856252877e1d4d0aacc5759f8d86f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2p_JZYlZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://effectivedev.com/conteimg/2019/08/Screen-Recording-2019-08-25-at-3.03.03-PM.gif)

好吧。所以我们升级了一些死角。你可能已经注意到的另一件事是，那里有另一个选项。您可以将按下 Caps Lock 键设置为“Escape”键。将“Caps Lock”设置为“Escape”值非常诱人。如果你有一台苹果的 Touch Bar MacBook Pros，“Escape”甚至可能是更好的选择。我这样做了一段时间，我可以像推荐“控制”选项一样推荐它。当然，我们可以做得更好。

> 苹果公司的旁注，如果你正在读这篇文章。我喜欢 Touch Bar。不过，在专业设备上的功能键上面*会做得更好。*

## 什么时候一把钥匙*比一把钥匙*多？

是时候介绍这篇文章的真正明星了，[登山扣元素](https://effectivedev.com/keyboard-hacks/)。自我描述为“一个强大而稳定的 macOS 键盘定制程序”，我决定对它进行测试。我喜欢这个结果，我想你也会喜欢的。

Karabiner Elements 非常类似于“修改键…”提示，就像 f1 赛车非常类似于自行车一样。在它的“简单修改”标签中，Karabiner 允许你将任何一个键切换成任何一个键作为*键。我说过这是“*简单修改*标签吗？*

> “强大的力量必然伴随着巨大的责任！”瑞普，本叔叔。

虽然总共有八个选项卡，但我们将讨论前三个(简单修改、功能键和复杂修改)。如果您愿意，您可以轻松地重复相同的修改，在按下时将 Caps Lock 键转换为 Control 键。

“功能键”标签非常有用，因为它允许你改变键盘上标记的功能。或许你不喜欢 Launchpad，想要一个普通的 F4，而不改变其余的？

“复杂修改”标签是事情变得疯狂的地方。如果一个键可以根据其使用的*上下文*做不同的事情会怎么样？这里有几个例子:

### 应用特定:

也许你的 IDE 强迫你使用`Shift + F4`,但是你没有使用`F4`做任何事情。你可以让登山扣强迫你 ide 里面的所有`F4`按键*变成`Shift + F4`。*

 *### 多修饰键:

如果你读过我在阿尔弗雷德上的帖子，你可能会注意到你可以通过键盘快捷键启动高级工作流程。如果你浏览了你的应用程序中的快捷键，你会注意到留给你灵活性的选项并不多。

例如，如果你一整天都在相同的五个应用程序之间循环，你的小手指可能会因为在应用程序之间切换而感到疲劳。让我们添加我们自己的:介绍临时超级键！

这个临时的超级键在技术上是一个`Control + Option + Command + Shift`的组合。按下`Control + Option + Command + Shift + C`打开 Slack 会有点烦人。好吧，让我们把一个键换成超键。在这种情况下，让我们设置正确的命令键作为我们的超级键。更新 Alfred 以使用键盘快捷键打开应用程序后，我进行了以下设置:

*   打开我的帽子。
*   `Hyper + K`打开我的 _K_anban 板。
*   `Hyper + G`打开我的 _G_itlab。
*   打开我的电子邮件。

实际上，你的记忆是这方面的软极限。

### 区分修饰键和单独按键:

这种定制是优雅的，可能是我最喜欢的一部分。一个键单独按下时可以是一个键，但用作修饰键时又是另一个键。例如，让我们将不合适的 Caps Lock 键设置为单独按下时 Escape，但与另一个键一起按下时算作 Control。

所以现在按下大写锁定键等同于按下退出键。然而，按下`Caps Lock + P`与按下`Control + P`是不明确的。想想 Vim 的可能性吧！

为此:

1.  在 Karabiner 中打开“复杂修改”标签。
2.  点击“添加规则”。
3.  单击“从 Internet 导入更多规则(打开 web 浏览器)”。
4.  当浏览器打开时，搜索“更改 caps_lock 键(rev 4)”。
5.  对于列出的选项，单击“导入”。一旦导入，它会显示所有可用的规则供您选择。
6.  找到“更改 caps_lock 以控制是否与其他键一起按下，如果单独按下则退出”，然后单击旁边的“启用”。

搞定了。

### 更加疯狂

你还可以做更多的事情。以下是一些例子:

*   双击一个键产生一个事件。
*   按住一个键会产生一个事件。
*   使用多个非修饰键产生一个事件。

## 登山扣的丑陋一面

我发现 Karabiner 的一个难点是它的复杂修饰语的[文档隐藏在 karabiner.json 参考手册菜单项下。一旦创建了复杂的修饰符 JSON 文件，就需要导入它。在撰写本文时，还没有办法直接导入 JSON 文件。要导入文件，您需要使用 URL `karabiner://karabiner/assets/complex\_modifications/import?url={URL}`，其中`{URL}`是 JSON 文件的完整路径，以`file:///`开头，URL 编码。](https://effectivedev.com/keyboard-hacks/)

比如，假设文件在`/Users/username/Documents/Modifier.json`，我们把它改成`file:///Users/username/Documents/Modifier.json`，然后编码成`file%3A%2F%2F%2FUsers%2Fusername%2FDocuments%2FModifier.json`，加上 URL 的其余部分作为前缀`karabiner://karabiner/assets/complex\_modifications/import?url=file%3A%2F%2F%2FUsers%2Fusername%2FDocuments%2FModifier.json`。

#### 示例 karabiner.json 文件

## 把一切都包起来

这些变化代表了我自己的需求。所以现在我们有:

*   移除了弊大于利的键盘快捷键；
*   打开应用程序和网页的单键盘快捷键；
*   将一些我们最常用的键移到更好的位置；和
*   设置我们的环境，以便能够做更多的事情。

我希望这篇文章给了你创建快捷方式的信心，并有助于加快你的工作。如果你已经创建了一些快捷方式，请在下面的评论中告诉我！

如果你喜欢这个内容，请订阅电子邮件简讯。此外，一定要在 Twitter 上关注 [@EffectiveDev](https://effectivedev.com/keyboard-hacks/) ，获取有用的链接和提示。非常感谢您的阅读，祝您度过美好的一周！*