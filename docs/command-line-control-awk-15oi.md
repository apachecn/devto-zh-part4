# 命令行控制:awk

> 原文：<https://dev.to/hartmann/command-line-control-awk-15oi>

除非您花大量时间主要在命令行上工作，`awk`是您可能遇到过几次，但从未真正学会如何正确使用的命令之一。类似于[我最近与`xargs`](https://thomashartmann.dev/blog/xargs-and-the-unruly-tags/) 的冒险，我最近发现了一个小用例，我可以从使用它中受益，所以我决定坐下来研究一下。

甚至比使用`xargs`更是如此，这是一个*真正的*强大工具，但让我们专注于基础，看看我们能利用它做什么。

# 什么是`awk`？

`awk`是使用 AWK 语言编写的程序处理文本的命令。

要理解它能做什么，理解它使用的语言可能是一个好的起点。让我们用阿尔弗雷德·艾侯的话来大致了解一下什么是 AWK 语(T1 ),他是这种语言的创造者之一。

> AWK 是一种处理文本文件的语言。文件被视为一系列记录，默认情况下，每一行都是一条记录。每一行都被分解成一系列字段，因此我们可以将一行中的第一个单词看作第一个字段，第二个单词看作第二个字段，依此类推。AWK 程序是一系列模式-动作语句。AWK 一次读一行输入。对程序中的每个模式扫描一行，对于每个匹配的模式，执行相关的操作。

这可能有点复杂，所以让我们来分析一下:

“AWK is a language for processing [..] text”

AWK is a *domain-specific language* (DSL) focused on text processing. The `awk` command expects that its first argument is a script or a string in this language.

“AWK reads the input a line at a time”

AWK is line-oriented and works through the input line by line. (It’s actually *record*-oriented, but the default separator is a newline character, so this is the same thing by default.)

“Each line is broken into a sequence of fields”

Each word in a line maps to a field. These fields are accessed with the `$` operator, e.g. `$1` for the first word, `$2` for the second, and so on. `$0` is the whole line. By default, fields are delimited by whitespace (which is why I’ve called them words) but this can be customized.

“An AWK program is of a sequence of pattern-action statements”

这意味着它是一系列带有动作的谓词。如果谓词评估为 true，则执行指定的操作。如果没有指定谓词，它将始终计算为 true，如果没有指定操作，它将默认打印整行。

*嗯？*是的，这仍然有点令人困惑，但也许一些例子会使它变得更清楚:

Patterns

这是检查每一行的谓词。它通常采用用正斜杠括起来的正则表达式的形式。`/foo/`、`/b[ar]?/`、`/^baz/`、`/(fizz|buzz)$/`都是例子。你拥有的大部分正则表达式技能在这里都是适用的(字符集，字符类，替换，等等。).

您还可以根据正则表达式匹配特定字段。只想匹配第二个字段包含“cheese”的行？`$2 ~ /cheese/`

模式也可以由函数和比较组成；因此，如果您希望只对非空的行进行操作:`length > 0`

如果没有给出模式，每一行都匹配。

Actions

这些命令告诉`awk`要执行什么。它们用花括号(`{}`)括起来。在这里，您可以指示`awk`打印字符串的某个字段，例如`print $3`，或者如果您正在计算单词，则递增一个计数器:`word_count += NF`(是的，我一会儿会讲到`NF`是什么意思)。

如果没有给出动作，`awk`将打印匹配行。

# 基本~~认证~~T0】

这是对语言结构的一个快速概述。在开始使用它之前，让我们先来探索一下它的一些特性。

## 内置变量

`awk`有许多内置变量，虽然我不会一一列举，但我发现这些是最有用的:

`NR`

给出当前匹配的行号。可用于将行号添加到输入文件:`awk '{print NR, $0}'`。或者也许寻找包含“冰激凌”的行更适合你:`awk '/ice cream/ {print NR}'`。

`NF`

这是一行中字段的数量。如果你正在寻找最后一个字段，或者找出一行中有多少个字段，或者查看它是否包含一个模式，这很有用:`awk '$NF ~ /out/ {print NR}`

`FS`

字段分隔符值。这是`awk`用来将每一行分割成字段的。默认情况下，这是空白。如果您有一个充满逗号分隔值的文件，并且想要用逗号而不是空格来分割每一行:`BEGIN {FS=","}`

`RS`

这相当于字段分隔符的行(“记录”)。默认为`\n`。假设您想将您的`PATH`打印成多行:`echo $PATH | awk 'BEGIN {RS=":"} {print}'`

## `BEGIN`和`END`

`awk`让您通过使用`BEGIN`和`END`提供在脚本开始和结束时执行的命令。虽然它们看起来很像，但它们一点也不特别。取而代之的是，把它们想象成分别在评估任何的*之前和评估所有*的*之后评估为真的模式。*

`BEGIN`可用于设置字段分隔符或初始化变量。

`END`用于打印出程序生命周期中的结果，如字数。如果我们带回我们的字数统计例子:`awk '{word_count += NF} END {print word_count}'`

## 函数和条件

像大多数语言一样，AWK 有许多内置函数(比如我们前面看到的`print`和`length`函数),如果你愿意，还可以让你定义自己的函数。对于大多数琐碎的操作来说，这可能是多余的，但在某些情况下可能会派上用场。

AWK 也有条件句！虽然您可以在 actions 中使用`if else`构造，但我想强调的是，您可以基于所提供的模式执行条件语句。即`awk '/foo/ {print "FOO"} {print "bar"}'`将为与`/foo/`匹配的行打印‘FOO ’,为不匹配的行打印‘bar’。

# 申请

既然我们已经知道了它是如何工作的，那么我们可以用它来做什么呢？让我们来看一些示例应用程序:

## 将文件中的行按长度排序

这里有一个好玩的小应用:我们拿一个文件，统计每行的
个字符的个数，然后根据
个字符的个数进行排序:

```
awk '{print length, $0}' <file> | sort -n 
```

如果您想排除空行，请尝试以下方法:

```
awk 'length > 0 {print length, $0}' <file> | sort -n 
```

## 友好`PATH`

这与上面列出的是同一个，如果您正在
寻找当路径在
一行时很容易丢失的某些条目，这是很有用的:

```
# bash, zsh
echo $PATH | awk 'BEGIN {RS=":"} {print}'

# fish
echo $PATH | awk 'BEGIN {RS=" "} {print}' 
```

## 计算字数

之前用过的另一个例子。统计一个文件或
任何输入流中的字数:

```
awk '{word_count += NF} END {print word_count}' 
```

## 解析 git 日志

也许您的团队已经同意，如果与任务相关，所有提交消息都应该以
`#<task_no>`开头，如果不相关，则以`#--`开头。为了找到与某个特定任务相关的
——比如说`#42`——我们可以这样做:

```
git log --oneline | awk '/#42/ {print $1}' 
```

或者找出属于某个任务的提交与不属于某个任务的提交的比率如何？

```
git log --oneline --no-decorate | \
awk '$2 ~ /#[0-9]+/ {task += 1} {notask +=1} \
  END {printf "Tasks: %d\nNo-tasks: %d\nTask to no-task ratio: %f", \
  task, notask, task / notask }' 
```

(是的，`printf`函数的工作方式与 C 语言中的非常相似——如此多的
,以至于我还没有去查阅它！)

## 杀人流程

这实际上是引发我去调查`awk`的原因，也是
最终导致了这篇文章的出现。

有很多方法可以从命令行杀死应用程序，
，有一段时间，我默认的退路是经典的`ps aux | grep
<app>`组合。

虽然这列出了所有相关的进程和它们的进程 id
(*PID*)，但是您必须手动复制 PID 并将其放入
`kill`命令来关闭它。这充其量是令人讨厌的，如果这个过程产生了我们想记为
好的孩子，情况会变得更糟。

我们该如何应对？嗯:

```
ps aux | awk '/<app>/ {print $2}' | xargs kill 
```

1.  `ps`列出系统上的所有进程

2.  然后，`awk`遍历包含应用程序名称的每一行，
    提取第二个空格分隔的单词——在本例中是 PID—
    ——并打印出来。

3.  对于最后一段，我们使用`xargs`将 PID 输入到
    `kill`命令中，从而终止所有进程。

这工作得很好，尽管它附带了一个警告，即它还会
尝试并杀死`awk`进程(因为应用程序名是
命令的一部分，所以它被`ps`列出)，但这只是一个小的
烦恼，我将把修复它作为一个练习留给读者。

现在，我确信你可以让`killall`做类似的事情，
，但是我发现这种方式更有效(我的意思是:*更接近我期望的*)。

# 关闭起来

*Phew* 。我们今天学到了很多，这篇文章比我最初想象的
要长得多——大约四五倍——但这是一段相当长的旅程，我很高兴你和我在一起，伙伴。我
希望你也收集了一些新的见解，并希望你能在你的日常生活中找到一些应用。

下次见！