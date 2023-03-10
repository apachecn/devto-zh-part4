# 维姆:9 分钟内从敌人变成朋友

> 原文：<https://dev.to/omerxx/vim-from-foe-to-friend-in-9-minutes-2np0>

### 3 年强化学习的教训

[![](img/1dbd0c16767f381957e792a8e0b53721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--paRSwcFn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2Afv2B1V17KY17nxO2S4-pew.png)

> **TL；T2 博士:使用 Vim 是迄今为止你所采用的最有效率、最愉快、最有回报的工具。这篇文章是我长久以来的一个想法；关于 Vim 的信息简直是无穷无尽，每次我开始写的时候，我都认为我只是在添乱。我觉得它变得太重要了，不容忽视，对生产力的改变太大了，可能是我决定学习的最好工具，所以我分享我的过程。这是一个关于我认为任何人都应该如何开始的固执己见的帖子。由于我通常更喜欢获得可靠的程序和实际的行动项目，这就是我试图在这里创建的，这样读者就不会迷失在信息的海洋中。**

多年来，维姆对我来说是个陌生人。在我大三的第一次求职面试中，招聘经理想测试我有限的技能，并问我最喜欢的 IDE 是什么。我的回答类似于“Notepad++”。不用说，这不完全是他所期望的。他进一步挖掘，问道:“你觉得维姆怎么样？”
我回答:“Vim？？这不就是控制台没人知道怎么退出的东西吗？”
长话短说，我没有得到那份工作…

对我来说，Vim 是视频中使用的 CLI 编辑器 sysadmins，是世界各地的时髦开发者使用的一种黑客工具。为什么有人会这样对自己？”——每当我遇到另一个热心的用户时，我都不止一次地问自己和别人，试图理解。我真诚地想知道为什么人们谈论它这么多，为什么会有人使用它？当 Vim 是一个如此糟糕的*工具时，怎么会有人有动力使用它并持续改进他们的流程呢？
*现代编辑怎么了？**

 *所以我试了一下。很多。我参加了很多很多次试驾活动。我想向自己证明我可以用它。这是我的报应，我不会接受失败。我开始意识到维姆是一种*宗教。*有一群人全心全意地相信它，也有同样多的反对者，像我一样看不到光明。所以我把 Vim 放在一边，但是当我注意到开发社区中的其他专业人士对它评价很高时，我不禁想起了它。见鬼，即使在硅谷 *[ [第三季第六集](https://www.youtube.com/watch?v=SsoOG6ZeyUI) ]* 上，争斗也是围绕空格和标签以及 Vim 和 Emacs 展开的。“真的吗？”我想，“Vim vs. Emacs？”，人家不用*标准*编辑器？

真正的改变发生在我遇到一个我合作过的最有才华的后端开发人员的时候。他对 Vim 如此热情，有如此多的知识，最重要的是，他工作得如此迅速和顺利，我不得不知道他在做什么。这导致了我的“为什么”的开始…

## 为什么

1.  Vim 总是出现在任何*nix 机器、Mac 上，很快甚至会出现在 Windows 机器上。你总是可以发现它在你的操作系统上运行。Vim ( [或者它的前身 VI](https://askubuntu.com/a/418413) )已经安装好了，所以即使你不打算把它用在所有事情上，你可能也想对它有所了解。您希望能够在远程服务器周围快速找到自己的位置进行编辑。

2.  **这是富有成效的**
    这是使用 Vim 的圣杯；一旦你掌握了基础，你会变得非常快，你真的无法回头。当你写信息或在线聊天时，你会开始寻找那些快速的动作。以一种你可能从来不知道的方式编写任何代码或编辑代码块变得流畅。

3.  Vim 是如此强大，它可以与你能想到的任何东西集成。我甚至没有把它留给 git 提交、推送、浏览历史等等。更进一步，[创建了我的解决合并冲突的流程](https://medium.com/prodopsio/solving-git-merge-conflicts-with-vim-c8a8617e3633)就来自 Vim！

4.  不确定我是否应该把这作为一个真正的因素，但是说实话，Vim 是一个令人愉快的工具。每次我使用它，包括编辑这些行时，我都喜欢我的快捷键、移动能力、宏和可重复的命令。
    想象自己在办公室使用它；你的同事会对你印象深刻，他们会开始寻求资源和建议。我知道，因为我就是这样开始的，我的其他同事也是这样开始的。在达到中等熟练水平后，我们都可以说同样的话:你变得如此*快得不可思议*，其他编辑不再有意义。你可以快速思考，快速工作，再也不会觉得自己是个白痴。

5.  **没错**
    毫无疑问，你有多少次发现自己在一个文件中编辑了 20 多个位置，一遍又一遍地触摸你的鼠标，心里知道你正在做的事情要么是错的？有多少次你觉得你在浪费时间去点击 UI 按钮或者寻找合适的点击来帮助你做出改变？对我来说，答案是几十次，如果不是几百次，直到有人决定停止它。

6.  **天很亮；它很轻，只占用你机器资源的 10%。查看下面描述流行 ide 平均内存消耗的比较:**

[![A comparison of VIMs average memory consumption taken on multiple Linux servers](img/a7097ac004354d55be984c95bd310cfe.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--DL-CbZsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2648/1%2ANUZNWjWqVLXvmxcNbbK23w.png) *多个 Linux 服务器上的 VIMs 平均内存消耗比较*

## 如何

老实说，Vim 并不是最容易采用的工具。见鬼，真是**难**。
Vim 有一个非常陡峭的学习曲线，但请放心:如果你尝试，不要放弃，并保持一致，这是 **1000%** 有益的。

[![Time vs. Productivity of IDEs](img/93d98d9ac1c2e779c6c42de7388b5f98.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--6t8eN7U_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AlnYWPVWZIcPFfDiRhfq1JQ.png) *时间与 IDEs 生产率*

上面的图片简单展示了我的学习曲线经历。使用任何其他 IDE，我很快变得半高效(“半”只是在知道 Vim 能做什么之后回想起来说的..).然而，绿色的 Vim 曲线显示了我开始时的挣扎，过了一会儿，它变成了一项令人难以置信的技能。

### 我是怎么做到的？

经过几次残酷的战斗，我夹着尾巴回到了我的后备 IDE，我决定真正学习 Vim。我是这样做的:

1.  我有一个可以随身携带的漂亮的小笔记本

2.  我买了德鲁·尼尔的《超赞实用 Vim 》(精装版)和我的 iPad，以便在旅途中阅读

3.  每天晚上睡觉前，我都会阅读一个提示——这本书非常聪明，适合轻松、缓慢的学习

4.  我的 mac 上的 ide 被移除了，再也没有 VSCode 或 PyCharm 的支持了。只有维姆一个人

5.  我已经注册了“[快速掌握 Vim](https://jovicailic.org/mastering-vim-quickly/)”时事通讯，并在[推特](https://twitter.com/MasteringVim)上关注他们，在那里我不断学习新的技巧

6.  我已经开始在网上列出我在路上学到和学到的技巧，我一直保持到今天
    [**谈到新的技巧**，它们是无穷无尽的](https://github.com/omerxx/vim-notebook/blob/master/VIM_NOTEBOOK.md):即使在工作多年后，你也会发现自己在为另一个你从来不知道的功能喝彩。Vim 是一个无限的糖果罐

[![Rotating the joke on VIM… don’t quit the fight; it’s worth it](img/4cf49c1a95c785e79cc5c7d36e7a4fac.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--VTmuQ1Rn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AsHY0Rr-QMCNmV_20.png) *旋转 VIM 上的玩笑…不要退出战斗；这是值得的*

### 渐进学习

经过几天的阅读，我慢慢开始使用著名的`hjkl`掌握 Vim 中的运动，并学会了搜索和保存击键。我开始理解 Vim *如何运作*。做每一个改变最快最优雅的方法是什么？我开始发现自己在阅读了一个尝试它的技巧并把它写在笔记本上后就起床了。

并非每个人都适合一头扎进去。如果你还不愿意放下一切，单独使用 Vim，你可能想尝试一些 ide 的优秀集成；一个受欢迎的是 VSCode 的 V im [模式](https://github.com/VSCodeVim/Vim)，它增加了你心爱的 Vims 的大部分功能和能力(很快就会被抛弃)😉)IDE。我试了一下，很难找到我习惯的 100%的功能，所以我直接回到了完整的 Vim。

## 当

问这个问题很有趣，但是——现在。你可能正在启动一个新项目或开始一项新工作；这些只是采用一种新的改变生活的工具的触发器。没有什么是戏剧性的；你可以边走边捡。只要你坚持不懈，下定决心，几周之内，你就会达到之前讨论过的不再需要其他工具的地步。你将会超越仅仅是高效的那一点:你将会在 vim 中比其他任何事情都要快。

## 谁

玩笑归玩笑，掌握 Vim 对任何作家来说都是改变人生的。是的，它是为编辑代码而设计的，但是我开始在那里写文章，因为我感觉更自信、更有效率、更舒服。这里有一个不错的作品:“ [Vim for writers](https://hackernoon.com/vim-for-writers-ee15d2a8f512) ”。

无论你是 IT 人员、开发人员、全职开发人员、临时编剧(和编剧😁)，或者数据分析师:每个人都可以使用 Vim，并最大限度地享受它。我不得不写 Python、Golang、Crystal、TypeScript Bash 和许多文本(例如，现在的这些行)。在我能想到的所有文本编辑场景中，vim 都是完美的解决方案。

## 定制

到目前为止，您已经了解到 Vim 是最通用、可定制的工具之一。它不仅有无穷无尽的插件，而且你还可以改变你的配置文件。vimrc)并且几乎可以改变一切。话虽如此，你可能想开始[香草](https://peteraba.com/blog/my-vanilla-vim-cheatsheet/)。[这里有一个很好的视频](https://www.youtube.com/watch?v=XA2WjJbmmoM)，只要知道 Vim 的内置能力，就能帮你省去很多插件安装。推荐使用的是 Tim Pope 的 Vim-sensible 插件。

### 插件

Vim 有一个最大的爱好者社区，有无穷无尽的插件，其中大部分可以在 GitHub 上找到。不过，提醒一句，虽然插件很有用，也很有趣，但它们也有一些缺点:

*   他们对 Vim 要求很高:你可能会注意到一些高要求插件的滞后和更长的响应时间

*   它们可能与其他插件/配置冲突:(通常，你不会注意到流行的插件)

*   他们掩盖了维姆的一些能力。你安装了一个在 Vim 中已经有很好的普通功能的插件——在运行每一个闪亮的插件之前检查一下自己

*   它们可能会减缓你的学习速度，小心翼翼地采用它们，慢慢地，只有当你觉得有必要的时候

这是我使用的插件的一个很短的列表(我试图通过删除那些我偶尔不再使用的插件来维护它们)。你也可以在我的中找到它们的完整列表。GitHub 上的 vimrc。

*   [**tpope/vim-Sensible**](https://github.com/tpope/vim-sensible)—上文“定制”中讨论的可感知配置

*   [**tpope/vim-surround**](https://github.com/tpope/vim-surround)—改变/创建/移除 VIM 中的周围环境
    :<'([

*   [**【tpope/vim】注释**](https://github.com/tpope/vim-commentary) —快速注释掉代码块

*   [**tpope/VIM——逃亡者**](https://github.com/tpope/vim-fugitive)——VIM git 集成——永远没有理由离开 IDE！

*   [**【VundleVim/vundle . vim**](https://github.com/VundleVim/Vundle.vim)—插件管理器

*   [**Valloric/YouCompleteMe**](https://github.com/Valloric/YouCompleteMe)——在生成代码行时，自动完成非常有用的集成

*   [**w0rp/ale**](https://github.com/w0rp/ale) —一个基本的静态代码分析

*   [安全气囊/vim 吉他](https://github.com/airblade/vim-gitgutter)

*   [**【June Gunn/vim-easy-align**](https://github.com/junegunn/vim-easy-align)—用分隔符对齐代码

*   [**vim-airline/vim-airline**](https://github.com/vim-airline/vim-airline)—可视化模式、字数、编码等的有用工具栏。

*   [**scroolose/nerd tree**](https://github.com/scrooloose/nerdtree)——一个流行但我自己很少使用的插件，像大多数 ide 一样显示索引树

*   [**June Gunn/fzf . vim**](https://github.com/junegunn/fzf.vim)—nerd tree 的更好的替代品，通常用于查找项目文件

*   [**air blade/vim-rooter**](https://github.com/airblade/vim-rooter)—设置项目的根目录，不管 Vim 是从哪里启动的(对 fzf 有用)

*   [**【Python-mode】/Python-mode**](https://github.com/python-mode/python-mode)—Python 静态代码分析(马上遵从所有 PEP，一个奇妙的插件)

*   [**/自动配对**](https://github.com/jiangmiao/auto-pairs) —自动方便地生成一对左右括号

* * *

## 到此为止，感谢你们走到这一步！

我希望这篇文章能帮助你开始并找到一条愉快的生产力改变之路。我知道互联网上充满了信息和想法，但由于我必须自己找出最有效的方法，并在几次尝试后才成功，我想与其他人分享这个过程，以寻求一些指导。我希望你喜欢阅读，并与任何想迈出 Vim 第一步的人分享！

非常感谢 [fuzzymidget](https://www.reddit.com/user/fuzzymidget/) Reddit 用户，他在风格和语言修正方面提供了帮助，在 subreddit `r/vim`上评论了[我的帖子](https://www.reddit.com/r/vim/comments/c91090/my_vim_journey_and_suggestions_in_a_blog_posts/)。

我邀请你看一眼我自己的。vimrc 配置，建议修改或取创意。

我叫 Omer，是一名工程师，在 [ProdOps](http://prodops.io) 工作，这是一家全球咨询公司，采用 DevOps 文化，以可靠、安全和简单的方式交付软件。请在下面的评论中告诉我你的想法，或者直接在 Twitter [**@** omergsr](https://twitter.com/omergsr) 上与我联系。

* * *

##### 编辑【七月五日】:

在他的允许下，添加了@russellbateman 的评论，这提供了另一个角度和一些我自己无法想到的见解:

> 我使用 Vim/vi 已经超过 35 年了。关于编辑的讨论(相当宗教化)有一个问题是民间只比较编辑。Vim/vi 不仅仅是一个编辑器；这是一个完整的，疯狂的文本处理器！单纯的编辑器，即使是像 Notepad++这样非常优秀的编辑器，通常也不会在文本处理的道路上走得很远。他们只是作为编辑出现。直到最近，提供 vi 兼容性的 ide 实际上只提供了模式化编辑的皮毛——VI 的这一部分让人们望而却步。他们没有提供。exrc 或。vimrc 支持的击键能力也不比 mode 深多少。许多人过去和现在都很困惑，为什么有人会自虐到学习 Vim/vi，只是为了在他们的 IDE 中打开它。
> 幸运的是，今天，虽然在集成方面展示了一些令人讨厌的做法，但像 JetBrains IntelliJ IDEA 这样的一流 IDE 提供了比二十年前愚蠢的早期 IDE 尝试更完整的 Vim 集成。就我个人而言，因为我是在 UNIX 命令行环境中长大的，所以我一点也不羞于在我的 ide 之外弹出一个控制台来运行 Vim/gVim，尽管我知道 IDEA 的 vi 仿真可能可以胜任。
> 最后，我通常从不安装任何插件，尽管我知道它们的实用性。我并不敌视插件，只是正如 vi/Vim 现在无处不在(也是在新推出的 Linux 系统上的唯一游戏)，插件并不是无处不在的，所以我甚至不去想它们，直到我在做一些真正专业的事情，而不是一个频繁的情况。*