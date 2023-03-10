# 我为什么以及如何用 RBENV 替换 RVM

> 原文：<https://dev.to/krtb/why-and-how-i-replaced-rvm-with-rbenv-23ad>

# 大意

和大多数人一样，我的早晨是这样开始的，我坐在电脑前，吃着培根鸡蛋奶酪百吉饼。然后，像每个人的早晨一样，它迅速向不同的方向做了一个艰难的右转。

在寻找一个有趣的开源项目的过程中，我发现自己正在开发 github repo。在查看他们的文档时，我发现他们使用了一种叫做... **Rbenv** 。啊？！

[![ANTIQUE SHOW](img/e015a6dc9c6b91673371555154a6e5e5.png)](https://i.giphy.com/media/RGpcV7COu58Tcn7UL6/giphy.gif)

在我们离开港口，开始驶向布满宝石的海洋之前，我想让你们，我忠实的船员们，知道这篇文章将解释什么是 RVM，然后是 Rbenv，为什么这两者都很重要，以及我必须经历的成功过渡的过程。

我看得太多了，恐怖片。第一季不错！第二季就没那么多了。

## 这个为什么

好吧，为什么 Ruby 的这些包管理器有关系呢？

首先，这里有一个关于*包管理器*的简短片段。

> “软件包管理器或软件包管理系统是一组软件工具，它们以一致的方式为计算机操作系统自动执行安装、升级、配置和删除计算机程序的过程。”

选项#1 -> [RVM](https://rvm.io/) ( *ruby 版本管理器*)

《RVM》主要是由韦恩·埃塞金(Wayne Eseguin)创作的，你仍然可以在 T2 的推特(twitter)上找到他，还有其他项目。RVM 的工作于 2012 年 2 月在[开始。](https://wiki.archlinux.org/index.php?title=RVM&action=history)

RVM 有一个快速的安装过程，并且不需要深入研究你的文件。这对那些刚起步的人来说很棒，简单的项目等等。

这不是一个比另一个好，就像任何技术一样，这是关于权衡利弊，将这些与你试图实现的目标相结合，并选择你想要使用的东西。相互竞争的技术往往可以共存，互相推动改进，以获得更多的用户。RVM 本身就很棒，但一旦你发现自己在大公司，你可能会开始问问题，并研究什么对你自己的工作流程最有效。

对 RVM 的抱怨大多与其安装过程的重量有关。

这里有一个广泛的解释，澄清了错误的信息，填补了关于 RVM 的“cd”功能的一些空白。

对这个问题的一个简单总结是，RVM 创建了一个新的函数，它侵入了 *cd* 命令，为你提供了自动切换 Ruby 版本的功能。

谁爱看黑客新闻？好吧，在 Rbenv 第一次发布后，这里有一个精彩的主题，似乎整个 Ruby 社区终于觉得有了一个安全的空间，可以在一个宣泄主题中释放多年来被压抑的挫折感。

下面是 HackerNews 的帖子，标题是:[“Rbenv，一个不引人注目的 rvm 替代品”](https://news.ycombinator.com/item?id=2874862)

[![SPILL THE TEA](img/bf2f75e5101a1a5bd8e4e81083dd1d85.png)](https://i.giphy.com/media/14vaeCWc35rGSI/giphy.gif)

以下是该帖子的一些亮点

> “恕我直言，ruby 是一种语言，而 rvm 是一个非常脆弱和危险的 shell 脚本黑客的集合。”

另一个

> “依我看，RVM 还没有做好发展的准备。每周，我都会遇到它所创造和期望的环境的问题/困惑。我绝不会把它放在任何接近生产的地方。”

最后一个

> “完全没有变更日志也没有帮助。我永远不知道新版本中是否会出现什么问题，几乎每个新版本都有一些小小的语义变化，会影响我的系统。”

现在，为了保持良好的姿态，我将包括一些支持 RVM 的评论，这也突出了为什么我相信大多数编码训练营已经将它添加到他们的课程中。

优诺牌

> 我相信你的话。对我来说，它节省了我很多时间。编辑:以防我在这里表现得很刻薄，我不是故意的。我的项目往往比较简单，rvm 节省了我很多时间。我确信对于更大的项目来说这有点孩子气——我只是想知道 rvm 是否有什么地方使它普遍不适合生产使用。**双重编辑:**“一堆”大概是言过其实了。rvm 对过渡到 1.9.2 有很大的帮助。我不确定它是否会提供更多的效用。

-我...。两个

> 这是一种经典的“快速，正确-选择一个”的辩论。如果节省时间更重要，也许你应该在生产中使用 RVM，如果正确性更重要，你可能不应该。归结起来就是你的错误有多昂贵。

...特雷斯

> 如果您只有一台服务器，并且可以编写大部分安装过程的脚本，这不是问题。当您有多台服务器时，rvm 不会节省您的时间。

<figure>

[![](img/713bb75c46401c003c566eaa1d9e1225.png)](https://i.giphy.com/media/110H2727GW5ZcI/giphy.gif)

<figcaption>If you haven't seen 'Spin City', get to it!</figcaption>

</figure>

选项#2 -> [RBENV](https://github.com/rbenv/rbenv) ( *ruby 环境*)

RBENV 的作者是萨姆·斯蒂芬森，他可以在 T2 的推特上找到。他目前在[大本营](https://basecamp.com/)工作。RBENV 的工作于 2012 年 8 月[开始](https://wiki.archlinux.org/index.php?title=Rbenv&action=history)。

rbenv 的一个主要优点是它更轻。我们的意思是，它不需要向你的计算机系统抛出太多的钩子。

[![SPONGEBOB HALLOWEEN HOOKS](img/80e9048f81a46818625bb2e8bdec509f.png)](https://i.giphy.com/media/SC3MNJTm2pC6s/giphy.gif)

下面是他们自己的 github repo 中的一些亮点，开发团队对这两种技术进行了比较，以及它们是如何进行比较的。

### rbenv 确实……

*   为指定特定于应用程序的 Ruby 版本提供支持。
*   允许您在每个用户的基础上更改全局 Ruby 版本。
*   允许您用环境变量覆盖 Ruby 版本。

### 与 RVM 相反，rbenv 不…

*   需要装入你的外壳。相反，rbenv 的 shim 方法通过向$PATH 添加一个目录来工作。
*   覆盖像 cd 这样的 shell 命令，或者要求快速破解。这很危险，而且容易出错。
*   有一个配置文件。除了想要使用哪个版本的 Ruby 之外，没有什么需要配置的。
*   安装 Ruby。您可以自己构建和安装 Ruby，或者使用 ruby-build 来自动化这个过程。
*   管理宝石集。Bundler 是管理应用程序依赖性的一种更好的方法。如果您的项目还没有使用 Bundler，您可以安装 rbenv-gemset 插件。
*   为了兼容性，需要更改 Ruby 库。rbenv 的简单性意味着只要它在您的$PATH 中，其他任何东西都不需要知道它。

[感谢 rbenv！](https://github.com/rbenv/rbenv/wiki/Why-rbenv%3F)

就我个人而言，在我做研究的这一点上，我非常确信是时候从我的一天中抽出最多一个小时来清理掉我系统中的 *RVM* 并运行 RBENV 的安装过程了。

但是为了教育的缘故，我将提供一些关于 RBENV 如何工作背后的技术细节的信息。

1)你运行一个命令，不管是 *Ruby* 还是 *Rake*
2)然后你的计算机将在目录中运行，寻找那个特定的可执行文件。

```
/usr/local/bin:/usr/bin:/bin 
```

3)有一个名为 **PATH**
的环境 4)它包含一个目录列表，看起来像上面的代码
5)每个目录都用一种颜色分隔，‘:’
6)这个过程将从左到右发生，让左边的对象优先于右边的对象

<center>**In walk the SHIMS**</center>

<figure>

[![THE SIMS](img/da375758e438cbe96a682891abc2f4b9.png)](https://i.giphy.com/media/xTk9ZS8Ury96MENgAg/giphy.gif)

<figcaption>no,no, no, not The Sims. The SHIMS!</figcaption>

</figure>

1) rbenv 在路径前面插入“垫片”

```
~/.rbenv/shims:/usr/local/bin:/usr/bin:/bin 
```

2) rbenv 使用 [*重新散列*](https://www.quora.com/What-is-rehashing-in-a-data-structure) 来维护目录中的命令
3)如果它们匹配整个系统中的 ruby 命令
4)这些命令可以是 irb、gem、rake、rails，当然还有 ruby

## 在哪里

在用一种技术替换另一种技术之前，您首先应该担心的是到目前为止您已经在该管理器中安装的包。

如果你像我一样，已经安装了 RVM，因为这是你的新兵训练营带你进入的方向，你不想在令人生畏的 3 个月的第二天搅动这个锅...

在你的终端上键入(*希望能痛击*):

```
gem list --local

```

然后，您将获得安装在本地系统上的 gem 列表:

```
*** LOCAL GEMS ***

actioncable (5.2.1, 5.0.7)
actionmailer (5.2.1, 5.0.7)
actionpack (5.2.1, 5.0.7)
actionview (5.2.1, 5.0.7)
active_model_serializers (0.10.7)
activejob (5.2.1, 5.0.7)
activemodel (5.2.1, 5.2.0, 5.0.7)
...
ETC!

```

然后，您会想要复制并粘贴到某个地方，供您以后参考。我不知道是否有一个更简单的方法来导出和导入你的宝石到一个新的包管理器，如 Rbenv，但我不知道它，它已经是上午 11 点！没时间了！

[![](img/222d31f82a82079c2cbd482d2750f2d7.png)](https://i.giphy.com/media/QYwE2RH3sQt59DU02j/giphy.gif)

下一步是从以下两个 RVM 命令中选择一个来删除管理器

1) rvm 内爆

2) gem 卸载 rvm

此外，如果您创建了特殊的脚本调用，请检查这些地方

<figure>

*   ~/.bashrc
*   ~/.bash_profile
*   ~/.轮廓
*   ~/.zshrc
*   ~/.邪恶

<figcaption>Personally, I just open my VScode environment, from my main directory. ['. code' in the terminal]</figcaption>

</figure>

## 该如何如何

好吧！所以我在我的终端上使用了“rvm 内爆”,老实说，这是因为我想一次完成所有的操作，而阅读这个命令是我应该尝试的第一个命令。

我一运行它，就看到了这个:

```
my-local-machine-location.../.rvm/rubies/ruby-2.5.1/lib/ruby/gems/2.5.0/gems/compass-core-1.0.3/stylesheets: Permission denied

```

因此，这些行继续打印出来，每一行都以“权限被拒绝”结束。

在控制它之后，我得到了...

```
Failed to completely remove /Users/kurt/.rvm -- You will have to do so manually.

```

很好。那就来硬的。

似乎当我第一次运行安装程序时，我做得不对，或者在某个时候我移动了文件。

是的，您也可以尝试运行“ *rvm 内爆-强制*”标志。在任何人开始对强迫任何事情感到恐慌之前，它也不起作用。

回到 StackOverflow...

[![A FEW MOMENTS LATER SPONGEBOB](img/dc8336171f9a5db0acec753e6c3e19ad.png)](https://i.giphy.com/media/vL45Gljql7rTG/giphy.gif)

找到了。如何从我的系统中删除 RVM ruby 版本管理器？

所以我执行了投票最多的命令，实际上是来自 RVM 自己的故障排除页面。

如何从我的系统中彻底清除 RVM 的所有痕迹，包括系统范围的安装？

好的，下面是我输入的行

```
/usr/bin/sudo rm -rf $HOME/.rvm $HOME/.rvmrc /etc/rvmrc /etc/profile.d/rvm.sh /usr/local/rvm /usr/local/bin/rvm

AND

/usr/bin/sudo /usr/sbin/groupdel rvm

AND

/bin/echo

AND

rm -rf /usr/local/rvm

```

当我运行下一次调用时，我得到这样的文件或目录不存在。

```
sudo rm /etc/profile.d/rvm.h
sudo rm /etc/rvmrc
sudo rm ~/.rvmrc

```

我运行了一些其他的删除命令，以确保没有任何东西留下，得到的回应是 RVM 不存在。

然后我进入我的本地文件，找到 RVM 的参考资料，并暂时把它们注释掉。

*   ~/.bashrc
*   ~/.bash_profile
*   ~/.轮廓
*   ~/.zshrc
*   ~/.邪恶

一旦我开始使用 RBENV，并确保没有严重损坏，我将返回并实际删除这些行。但我从经验中学到的是，永远不要扔掉某样东西，除非你已经测试过，并认为你不再需要它了。这就是为什么我有大约 50 个标签...

运行以下命令检查所有隐藏文件

```
ls -a ~ 
```

<figure>

[![I TAKE NAME RIGHT HERE](img/99159fa89c06f1c5ecece433afedd277.png)](https://i.giphy.com/media/HwmDZaI4YEeZ2/giphy.gif)

<figcaption>I know I know, I'm tired too</figcaption>

</figure>

### 现在该安装 RBENV 了(:

这实际上并没有预期的那么长。

需要记住的问题:

*   安装了[自制软件](https://brew.sh/)！这篇文章已经很长了，所以你可以点击链接浏览一下。
*   在将命令粘贴到 bash 文件中之后，总是要重启终端。即使你认为你不需要它，给它一个干净的开始也是好的。

也进行设置演练的有用链接:

*   来自 traumverloren 的这个要点
*   [Thoughtbot 也使用 rbenv](https://thoughtbot.com/blog/using-rbenv-to-manage-rubies-and-gems)
*   [数字海洋及其 rbenv 设置流程](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-macos)

1)更新自制软件

```
brew update 
```

2)然后通过自制软件安装 rbenv】

```
brew install rbenv 
```

3)这部分有点奇怪，因为我认为它不起作用。去你的。bash_profile/。bashrc 文件并添加到

```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)" 
```

如果你使用的是 ZSH，那么只需将`eval "$(rbenv init -)"`粘贴到你的。zshrc 文件

好吧，这是我自己的地方。“rbenv init”只是打印出关于如何集成 shell 命令的指令。这就是我们在步骤 3 中所做的。所以当你读它，运行它，得到同样的指令，不要害怕。

5)运行此程序，检查所有安装是否正确

```
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash 
```

您应该会看到类似
的内容

```
Checking for `rbenv' in PATH: /usr/local/bin/rbenv
Checking for rbenv shims in PATH: OK
Checking `rbenv install' support: /usr/local/bin/rbenv-install (ruby-build 20190828)
Counting installed Ruby versions: 1 versions
Checking RubyGems settings: OK
Auditing installed plugins: OK 
```

耶！

好了，现在让我们看看红宝石列表

```
rbenv install -l 
```

让我们安装最新的。请务必在他们的[网站](https://www.ruby-lang.org/en/downloads/)上查看最新版本的 Ruby。

然后只需添加您希望全球可用的默认版本。

<figure>

```
rbenv install 2.6.4 
```

<figcaption>above ruby version as of August 2019</figcaption>

</figure>

此外，你可能想要安装捆绑器来捆绑和安装 gems

```
gem install bundler 
```

<center>We did it!!!</center>

<figure>

[![GET DOWN FRIDAY, TOY STORY, HARRY POTTER](img/afa8eaf0ba9f5072d61874aa5249bfcd.png)](https://i.giphy.com/media/1kTKRsMWY44MAlV2QW/giphy.gif)

<figcaption>Ah yes, I can feel the joy</figcaption>

</figure>

## TLDR；

我研究了 RVM 和 RBVENV 是什么，并研究了它们的优缺点。归结起来就是工作速度和质量。之后，这篇文章的第二部分专门讲述了我自己的拆卸/安装步骤。希望这能帮助一些人，我知道当我着手做这件事时，它会派上用场。如果没有，总会有互联网和 stackoverflow！

演职员表:
[unsplash:🇵🇭](https://unsplash.com/@charlesdeluvio?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Charles 🇵🇭")