# 使用正则表达式过滤服务器日志

> 原文：<https://dev.to/heroku/using-regular-expressions-to-filter-your-server-s-logs-2476>

你喜欢我对正则表达式(RegEx)的介绍吗？让我们用一些来做一些实际的工作。

# 这里有一个经典问题

您运行一个 web 服务，并且所有用户的性能都很差。出于我不会深入探讨的原因，您可能怀疑单个用户可能会导致问题。好消息是，进入服务器的每个请求都会被记录。坏消息是，你的服务很受欢迎，在过去的一个小时里，你有超过 1500 个请求。

[![](img/ba75f6aad56b88940333e0ab72f58850.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tSXvDkUD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gu0wna785fmeh5cn7054.gif)

你能找出这些日志中最常出现的 IP 地址吗？

狩猎开始了！让我们找出日志中出现频率最高的 IP 地址。

> 想看看我们要构建的命令吗？我保证在这篇文章结束时，所有这些都会变得有意义。

# 首先我们需要填充我们的日志

在这个例子中，我们将使用 Heroku 上托管的 web 服务。虽然 Heroku 通过附加组件为您提供了处理 web 日志的超级有用的生产服务(如设置警报、过滤器等)，但我们将绕过所有这些，转而进行一些 CLI 黑客攻击。

> 在整个教程中，我们将使用命令行界面(CLI)来查看我们的 Heroku 日志和运行我们的正则表达式过滤。在我使用的 shell 版本之后，我通常将这个接口称为“CLI”或“bash”。不熟悉 CLI？我写了一点关于它的东西

为了好玩，我们真的想填满我们的 Heroku 日志。我写了一个演示应用程序来实现这一点，所以你需要做的就是:

1.  [创建一个 Heroku 账户](https://signup.heroku.com/)
2.  安装 Heroku CLI
3.  运行`git clone https://github.com/tobyfee/log-filler.git`
4.  使用`cd log-filler`进入您刚刚创建的目录
5.  创建一个新的 Heroku 应用程序`heroku apps:create logfiller`(你必须把名字“日志填充器”改成你想要的名字)
6.  将应用程序代码发送到 Heroku，并使用`git push heroku master`运行一次

您可以通过运行`heroku logs`来测试一切是否正常

[![](img/7d5fbde326a5d414da2a80f417832576.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iEBJwM1n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r0lcpez5dgsdzd7uo0q2.jpg)

*呜呜呜，圆木！那些白色的数字和点串就是我们要找的 IP 地址*

> 重要提示:我建议抓取几十行代码，粘贴到您最喜欢的代码编辑器中。当我们编写正则表达式时，最好有一些可以引用的东西，而不必重新运行这个命令。

如果您向上滚动一点，您会注意到使用这个命令我们只能得到大约 100 行日志。这是 Heroku 的默认设置，但是使用`heroku logs -n [number]`你可以获得更多信息。

现在我们正在查看日志，让我们暂停一下，讨论两个基本点:

# 当你写一个正则表达式时，你的源材料中的内容很重要

从根本上说，正则表达式是关于在文本中寻找东西的。当我们评估一个正则表达式是否做了正确的事情时，我们需要考虑源材料。如果我们搜索的日志除了 IP 号之外没有其他的数字*，那么正则表达式只能匹配数字(也许抓取两组数字之间的任何内容都可以)。检查上面的日志，你会发现日志中有很多不是 IP 地址的数字。*

 *# 有许多方法可以找到同样的东西

有时两个正则表达式模式做*完全*相同的事情`[abc]`和`[a-c]`是相同的，在它们之间进行选择的唯一原因是哪个更可读。

其他模式工作方式类似，但功能差异很小。图案`/ab{2]a/`和`/ab*a/`都会匹配‘ABBA’；但是只有第二个模式也匹配“abbbbbbbba”

根据您已经了解的内容，您可以编写一个查找电子邮件的模式，如下所示:

`[a-z0-9]*@.*\.[a-z]{3]`

用英语写出来:

*   任何字母或数字任何次数
*   一个@符号
*   `.*`任意字符任意次数
*   `\.`一个圆点
*   `[a-z]{3}`任何字母三次

太好了！这应该涵盖…大多数电子邮件，对不对？然后，您可能会意识到@可以包含一个点之前的位，因此您可能会在模式的第一部分`[a-z0-9\.]*`添加一个点。很快可能会出现更多的异常，直到您发现您的 RegEx [电子邮件模式长达数千个字符](https://stackoverflow.com/questions/2245282/what-is-the-longest-regular-expression-you-have-seen)。

好吧，回到手头的任务。

# 我们来找一些 IP 吧

我们已经确定`heroku logs -n 1500`将获得一堆最近的日志行(准确地说是 1500 行)，我们如何在输出中使用正则表达式呢？使用 unix 管道的时间`|`

我不打算太深入地解释管道是如何工作的，目前我只将其定义为“将命令的输出 send it(就像我不知道*通过管道*引导它)作为下一个命令的输入。许多键盘上的竖线与\

我们想把`heroku logs`的输出发送到 grep，这是一个用正则表达式模式过滤东西的工具。`heroku logs -n 1500 | grep`会让 grep 过滤日志。如果你运行这个命令，grep 会抱怨，因为你没有给它一个模式。我们给它一个非常简单的模式怎么样？回头看看我们的日志。注意到请求的文件名是如何出现在末尾的吗？让我们过滤日志，找到那些请求“err.png”或其他变量的日志。

`heroku logs -n 1500 | grep 'err'`

这里我们使用最简单的 RegEx 模式:寻找三个精确字符的文字。

您应该得到一串包含“err”的日志行:

```
 2019-07-28T12:27:44.077022+00:00 app[web.1]: 10.0.0.153 - -  "GET /cgi-bin/mailgraph.cgi/mailgraph_3_err.png HTTP/1.1" 200 5554

2019-07-28T12:27:44.077057+00:00 app[web.1]: 10.0.0.153 - -  "GET /cgi-bin/mailgraph.cgi/mailgraph_0_err.png HTTP/1.1" 200 6324

2019-07-28T12:27:44.077063+00:00 app[web.1]: 10.0.0.153 - -  "GET /cgi-bin/mailgraph.cgi/mailgraph_2_err.png HTTP/1.1" 200 7001

2019-07-28T12:27:44.077067+00:00 app[web.1]: 10.0.0.153 - -  "GET /cgi-bin/mailgraph.cgi/mailgraph_1_err.png HTTP/1.1" 200 6949

2019-07-28T12:27:44.077078+00:00 app[web.1]: 10.0.0.153 - -  "GET /cgi-bin/mailgraph.cgi/mailgraph_3_err.png HTTP/1.1" 200 5554 
```

好了，现在我们开始过滤！只有一个问题，因为我们试图只获得 IP 地址。现在，当 grep 在日志行中发现' err '时，它会给出整行。我们只希望*和*行中正则表达式实际匹配的部分。我们可以通过给 grep 添加一个选项来做到这一点，`-o`我一直记得这个选项是'**o**only the matched part

返回一些非常无聊的输出，但这是我们在找到 IP 时想要的行为。

# 等等，一个 IP 地址长什么样？

当我们编写 RegExs 来验证用户输入时，这可能会让我们犯很大的错误，就像我的电子邮件示例一样。在这种情况下，我们可以使用简单的 ipv4 定义，即“四组数字，用点分隔”。让我们写我们的正则表达式！

> 将您输入的命令保存到单独的文本文件中可能会有所帮助。按键盘上的向上箭头将加载您的最后一个 bash 命令。这些东西很难编辑，最好能有你以前版本的日志。

每个命令请求 1500 个日志行需要一段时间，所以在接下来的几个版本中，我只请求最后 50 个。首先让我们过滤数字:

`heroku logs -n 50 | grep -o '[0-9]'`

我们返回(只显示最后几行)

```
 2

0

0

0

0

1

0 
```

为什么它不给我们每行的所有数字？虽然有一些 grep 模式可能会让我们接近这一点，但我们的模式只要求一个位数。让我们说“任何数量的连续数字”

`heroku logs -n 50 | grep -o '[0-9]*'`

```
 2019

2019

2019

2019

2019 
```

这里发生了什么事？同样，我们可以给 grep 一些配置，这可能会改变这种行为，但 RegEx 将希望匹配它找到的第一个东西。因为在每一个日志行中，年份值都在其他值之前，所以这就是我们所得到的。

从这里开始有几种方法，我们可以写一个完整的东西来说“不匹配日期”，但是如果我们更进一步说“任何数字后跟一个点”，那就没有必要了

`heroku logs -n 50 | grep -o '[0-9]*\.'`

(因为正则表达式模式中的点表示‘任何字符’,我们需要对它进行转义以表示‘实际的点’,所以我们使用`\.`)

我们得到的最后几行是:

```
 .

64.

242.

88.

1.

1.

1. 
```

等等，什么？为什么我们得到一些光秃秃的点，我们不是说“任何数字后面都有一个点”吗？这就是`*`的麻烦。原来‘这个字符的任意数’可以为零。有很多方法可以解决这个问题，但是一个简单的方法就是说“一个数字，然后是任何数量的数字”

`heroku logs -n 50 | grep -o '[0-9][0-9]*\.'`

```
 20.

64.

242.

88.

1.

1.

1. 
```

哦，你好，看起来不错！

# 展翅

因为我们将 ipv4 地址定义为“由点分隔的四组数字”,所以我们可以将我们的模式扩展为

`heroku logs -n 50 | grep -o '[0-9][0-9]*\.[0-9][0-9]*\.[0-9][0-9]*\.[0-9][0-9]*'`

> 好吧，好吧，等等，我们不可能一直敲`[0-9]`吧？

在我之前的教程中，我跳过了[字符类](https://www.regular-expressions.info/refshorthand.html)，但是为了让我们的图案更加整洁，我们应该认识其中的一个:`\d`，我记得它是‘任何数字’。我们可以用它来代替所有的那些`[0-9]`

所以我们的模式变成了:

`heroku logs -n 50 | grep -o '\d\d*\.\d\d*\.\d\d*\.\d\d*'`

果然我们得到了好看的 ip 地址

```
 10.0.0.153

10.0.0.153

10.0.0.153

10.0.0.153

10.0.0.153

10.0.0.153

10.0.0.153

10.0.0.153

10.0.0.153

64.242.88.10

64.242.88.10 
```

# 现实检查的时间

查看我们的示例日志，*似乎*我们正在匹配每行中出现的 IP 地址。但是我们怎么知道我们没有遗漏一些行呢？让我们通过将我们的输出传送到另一个命令`wc`来做一个现实检查。wc 在我们的输出中计算字数，但是`wc -l`计算行数:

`heroku logs -n 50 | grep -o '\d\d*\.\d\d*\.\d\d*\.\d\d*' | wc -l`

这将返回我们与正则表达式匹配的行数:`49`

哦不，哦天啊，那条不见了的线怎么了？！？！_ 尝试运行:

`heroku logs -n 500 | grep -o '\d\d*\.\d\d*\.\d\d*\.\d\d*' | wc -l`

您将看到我们有 499 个匹配项，因此我们似乎只缺少了一行。

如果你用`heroku logs -n 5`看一下最后几行，你会看到真正发生了什么。最后一行是:

2019-07-28t 18:00:20.143268+00:00 heroku[web . 1]:进程以状态 0 退出

哪个没有 IP 地址:)

> 好的，很好，但是我厌倦了一直打字

感觉很尴尬，当然有一种方法可以说“至少一个数字，之后你想说多少就说多少”？有！`+`！现在的格局只是`'\d+\.\d+\.\d+\.\d+'`

> 这里有一个小问题，`+`是 regex 的“扩展”部分，默认情况下在 grep 中不可用，与-o 一起，我们需要添加-e 选项来使它可用，所以我们的最后一个命令是`heroku logs -n 50 | grep -oe '\d+\.\d+\.\d+\.\d+' | wc -l`

# 将数据转化为知识

好了，结束就在眼前，让我们请求最后 1500 个日志行，使用 grep 和我们的 RegEx 将每一行削减到 IP 地址，`sort`地址，然后使用`uniq`计算日志中出现了多少个地址。

> 我不打算深究`sort`和`uniq`。这篇文章已经超级长了

`heroku logs -n 1500 | grep -oe '\d+\.\d+\.\d+\.\d+' | sort | uniq -c`

我们的产量呢？

```
 336 10.0.0.153

   1 12.22.207.235

  27 128.227.88.79

   7 142.27.64.35

  49 145.253.208.9

   4 194.151.73.43

   7 195.11.231.210

   1 195.230.181.122

  12 195.246.13.119

   4 200.160.249.68

   1 200.222.33.33

  13 203.147.138.233

  20 207.195.59.160

   4 208.247.148.12

   4 20964.242.88.10

   1 212.21.228.26

  28 212.92.37.62

   2 213.181.81.4

  24 213.54.168.132

   1 216.139.185.45

   2 219.95.17.51

   1 4.37.97.186

  28 61.165.64.6

   6 61.9.4.61

 879 64.242.88.10

   1 64.246.94.141

   1 64.246.94.152

   2 66.213.206.2

   3 67.131.107.5

   4 80.58.14.235

  21 80.58.33.42

   1 80.58.35.111 
```

每行左边的数字显示了该行显示了多少。

哦，好了，现在我们已经统计了 IP 地址，最大的罪犯已经很清楚了。为了整洁起见，让我们通过在末尾添加第二个排序命令来按计数排序

`heroku logs -n 1500 | grep -oe '\d+\.\d+\.\d+\.\d+' | sort | uniq -c | sort`

最后几行显示

```
 28 212.92.37.62

  28 61.165.64.6

  49 145.253.208.9

 336 10.0.0.153

 878 64.242.88.10 
```

64.242.88.10，你这个卑鄙的无赖。你把超过一半的请求发送到我们的服务器。现在我们看到你了。

# 最终清理

很好，但是我们能把它缩短吗？毕竟位`\d+\.`重复了三次…当然我们可以进一步压缩它:

`'(\d+\.){3}\d+'`

哇，现在我们查找 IP 地址的模式真的很紧凑，而且可能更具可读性！我要强调的是，这种变化对正则表达式没有任何功能上的影响，如果你有一个模式

这是一次大冒险，我还有很多想说的！如果你喜欢这个作品，让我知道，我会继续这个系列。*