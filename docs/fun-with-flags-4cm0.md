# 旗帜带来的乐趣

> 原文：<https://dev.to/quinncuatro/fun-with-flags-4cm0>

首先，做些家务。我已经可以说，想出一个每日帖子将比我的好友 Alex 的 SVG 工作更难。他每天都要完成一部动画。我整个月都在朝着一个更大的目标努力。

然而，我会尽我所能分享我每天学到的东西，即使是很小的东西。

* * *

#### 背景

我从 LeanPub 上买了 Nigel Poulton 的书籍包，并开始钻研，但最近学到了一些有趣的东西，我想我会分享一下。

当我还在法院的时候，我就在自学如何像在项目中实现容器一样快速地使用它们。在学习如何管理多套容器时，我和我的大学朋友讨论了他们使用的工具。听到每个人都说同样的话，我很震惊:

> 您通常从 CLI 命令开始，并根据需要逐步升级到具有更多抽象层的工具。接下来是 T1，接着是用 Bash 脚本自动化几个命令，最后是 Kubernetes。

当我将一个遗留应用程序放入容器时，我不得不多次重启我的容器。这让我学会了如何利用 Bash 脚本来自动化我生活中的许多痛苦。我通过拼凑在 StackOverflow 上找到的不同片段自学。这很好，但是我从来没有读过一本书或者其他什么东西来真正把基础知识记下来。

在我的新工作中，我使用 Bash 来自动化一些过程，并且我从与其他开发人员相处中学到了很多。[旁注:作为一个人的政府开发店变得非常孤独。]

#### 任务

我最近给自己安排了一个任务，开发一个工具来自动化一个过程，这个过程花费我们三个工程师的团队每天大约 0.5 - 1.5 个工时。在我们准备生产部署时，它涉及到大量的 Git 操作。几乎每一步都需要有人盯着。太乏味了。

幸运的是，我能够将一个同事正在编写的脚本放入 Docker 容器中，并以此为基础。这个版本仍然需要我们中的一个人反复运行，直到我们的任务列表完成。这是行不通的——这需要全程自动化，并且需要实际的错误处理来让我们知道什么时候出了问题。

构建完成后，我有了一个不错的小 CLI 工具，它有一些不同的标志，可以根据手头的任务改变脚本的运行方式。我曾为法庭制作过这样的小东西，从来没有真正遇到过任何问题，因为我只是在为自己制作工具。

我的同事看了一眼，然后问**“为什么这些标志需要有特定的顺序？”**

坦白地说，我从未考虑过这一点。有六面旗帜，但并不是每次都用得上。如果没有按照正确的顺序添加它们，脚本将会中断，Git 分支提交树可能会受损。

不太好。

#### 解

我很快就熟悉了 Bash 开关以及它们带来的所有乐趣。我已经专业地写了几年代码，所以我知道在谷歌搜索后设置一个快速切换，但是我不知道处理错误的正确方法。

```
#!/bin/bash

# Basic switch to handle a couple of flags
case "$1" in a )
    printf "Option A \n"
    exit 1
    ;;
  b ) 
    printf "Option B \n"
    exit 1
    ;;
esac 
```

Enter fullscreen mode Exit fullscreen mode

在 CLI 工具中使用开关来处理标志会出现各种错误:

*   用户可能只是不知道调用工具的正确方式。
*   用户可以输入您不想让他们使用的标志。
*   标志可能需要用户没有提供的额外参数。

嗯，第一个修复很简单。我们只是添加了另一个标记来打印帮助文本:

```
#!/bin/bash

# Basic switch to handle a couple of flags, with added help flag
case "$1" in h | help )
    printf "HELP MESSAGE \n"
    exit 1
    ;;
  a )
    printf "Option A \n"
    exit 1
    ;;
  b ) 
    printf "Option B \n"
    exit 1
    ;;
esac 
```

Enter fullscreen mode Exit fullscreen mode

让我们确保这是可行的:

```
hquinn$ ./example.sh -a

Option A

hquinn$ ./example.sh -b

Option B 
```

Enter fullscreen mode Exit fullscreen mode

接下来的两个修复实际上是我的新花絮发挥作用的地方。我们需要让我们的用户以他们想要的任何顺序输入标志。我们还需要他们能够用他们的旗帜来输入参数。

想想命令`yum install git`。

`Yum`是你的工具，`install`是你的“旗帜”，`git`是你用旗帜传递给工具的参数。

我了解到，在 while 循环中包装您的案例后，您可以使用内置的 Bash 函数`getopts`来限制甚至可以进入您的 switch 的标志类型:

```
#!/bin/bash

# Switch to let users place flags in any order they want
while getopts ":hab" opt; do
  case ${opt} in h )
      printf "HELP MESSAGE \n"
      exit 1
      ;;
    a )
      printf "Option A \n"
      ;;
    b )
      printf "Option B \n"
      ;;
  esac
done shift $((OPTIND -1)) 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行它时会发生什么？

```
hquinn$ ./example.sh -h

HELP MESSAGE 
```

Enter fullscreen mode Exit fullscreen mode

好吧，酷。现在我们可以在开关中使用`${opt}`变量，将我们放入不同的情况。while 循环实例化器中的`":hab`位允许我们限制允许哪些标志进入开关。如果该标志不在列表中，我们的开关不会启动，代码也不会运行。很简单。

在标志限制列表中的任何字母之后，我们可以放置另一个`:`来表示特定的标志需要一个附加的参数:

```
#!/bin/bash

# Switch to let users place flags in any order they want
while getopts ":hab:" opt; do
  case ${opt} in h )
      printf "HELP MESSAGE \n"
      exit 1
      ;;
    a )
      printf "Option A \n"
      ;;
    b )
      printf "Option B \n"
      newVar=$OPTARG
      printf "Argument is $newVar  \n"
      ;;
    : )
      printf "Invalid option: -$OPTARG requires an argument \n" 1>&2
      exit 1
      ;;
  esac
done shift $((OPTIND -1)) 
```

Enter fullscreen mode Exit fullscreen mode

我们去看看。

```
hquinn$ ./example.sh -b whatever

Option B

Argument is whatever

hquinn$ ./example -b

Invalid option: -b requires an argument 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经发现了上一个示例中的额外情况。当我们设置一个需要添加参数的标志时，Bash 为我们提供了一种捕捉错误的方法。只有当需要添加参数的标志(在本例中为`-b`)实际上没有被提供额外的参数时，`:`情况才会运行。

最后，如果用户输入了一个不在我们允许的标志列表中的标志，我们需要能够打印出一个有用的错误。

> "亨利，巴什也给了我们这样做的工具吗？"

你打赌它是。

如果在调用此 CLI 工具时提供了无效选项，则 opt 变量将被赋予值？。让我们添加一个案例来处理这个问题:

```
#!/bin/bash

# Switch to let users place flags in any order they want
while getopts ":hab:" opt; do
  case ${opt} in h )
      printf "HELP MESSAGE \n"
      exit 1
      ;;
    a )
      printf "Option A \n"
      ;;
    b )
      printf "Option B \n"
      newVar=$OPTARG
      printf "Argument is $newVar  \n"
      ;;
    \? ) 
      echo "Invalid option: $OPTARG" 1>&2
      exit 1
      ;;
    : )
      printf "Invalid option: -$OPTARG requires an argument \n" 1>&2
      exit 1
      ;;
  esac
done shift $((OPTIND -1)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果用户传入一个我们不希望他们传入的标志，他们会收到一条错误消息，提醒他们这一事实。

```
hquinn$ ./example.sh -c

Invalid option: c 
```

Enter fullscreen mode Exit fullscreen mode

原来在 bash 中可以用标志做很多事情。如果您有一个脚本，每次运行时都有一点不同，请考虑在其中添加一些标志逻辑。正确设置需要几分钟时间，并且需要多次尝试。然而，它通过有用的控制台消息为您节省的时间是无价的

* * *

我是亨利·奎因，这是关于旗帜的有趣话题。保持冷静。

[https://Henry needs . coffee](https://henryneeds.coffee)
[博客](https://henryneeds.coffee/blog)
[LinkedIn](https://linkedin.com/in/henryquinniv)
[Twitter](https://twitter.com/quinncuatro)