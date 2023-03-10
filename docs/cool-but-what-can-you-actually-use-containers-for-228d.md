# 如何将集装箱融入您的日常工作

> 原文：<https://dev.to/quinncuatro/cool-but-what-can-you-actually-use-containers-for-228d>

这周我们要回避一下。

我正计划将一个 Docker-Compose 项目转换成 Kubernetes 项目，但是这周我突然有了一个小项目。那个项目需要我设置一个容器来模拟生产环境，这样我就可以测试我的脚本，而不会干扰我们的任何客户。

我认为一个集装箱如何拯救世界的实际例子比我只是为了学习而学习更好。

* * *

每隔一段时间，我就会被赋予一项任务，一旦向我解释清楚，它似乎会花费我很多时间。每次这种工作出现时我都要花上几个小时。

我通常会问这个任务在给定的季度中出现的频率。如果一个季度的总时间超过一两天，我通常会把 XKCD 关于自动化的漫画扔出窗外，只管去做吧。

[![](img/5aa88a7cf52f5a4ac457b834888c8a91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GnOVKxdn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/knw35hwx3qoyid9z0co8.png)

> 图片来源:[https://xkcd.com/1319/](https://xkcd.com/1319/)

我花时间编写和维护一个自动化脚本，比让我离开“战斗”几个小时去处理一些平凡的事情，而我团队的其他人在处理生产问题，更值得。

本周，我接到一个任务，要求我跳上一台服务器，有计划地清理一些文件。有时它只是循环遍历一个列表，并使用一些简单的`*`匹配来处理一些东西。有时它需要一些 sed 魔法来使事情工作。

一旦我决定自动化这项任务是值得的，我就用 VS 代码将 Confluence 概要步骤复制到一个新文档中。我根据需要完成的类似工作将任务进行了分类，使用了一些 Bash 脚本，哭了一会儿，最终得出了以下 GitHub repo 的更复杂版本:

> 项目代号:[放出猎犬](https://github.com/Quinncuatro/ReleaseTheHounds) < - GitHub 链接

现在让我们深入了解那里到底发生了什么。

自述文件主要是告诉您如何启动容器并运行`./code/`目录中的脚本:

```
$ docker pull centos:5.11

$ docker run -v `pwd`/code/:/var/theHounds/ --name DogHouse -it centos:5.11 /bin/bash

$ cd /var/theHounds/

$ ./SetUpEnv.sh

$ ./ReleaseTheHounds.sh 
```

Enter fullscreen mode Exit fullscreen mode

"但是亨利，你为什么要用容器来装这个？"

很棒的问题！目前我有三个答案给你:

1.  像许多公司一样，我们在生产中运行 Red Hat Enterprise Linux。启动 CentOS(RHEL 的下游项目)服务器使我能够接近我希望脚本运行的环境。这给了我一个比 Windows 更相似的测试环境。

2.  容器应该是无形的。`./code/`，`SetUpEnv.sh`中的一个脚本创建了目录和文件供我测试。如果我需要再次测试，我可以重启容器，我的设置脚本每次都会创建相同的 env。

3.  容器不能访问您没有明确授予它们访问权限的任何本地存储。这样，如果我弄乱了我的脚本，我就不会不小心删除了我想保留的本地目录。

```
# EXCERPT FROM SETUPENV.SH:
#!/bin/bash
mkdir -p /opt/hoodlums

touch /opt/hoodlums/2019-03-07_Thief.zip
touch /opt/hoodlums/2019-06-12_Ruffian.zip
touch /opt/hoodlums/2019-07-25_Vandal.zip 
```

Enter fullscreen mode Exit fullscreen mode

因此，一旦我启动了我的容器并运行了这个脚本，我将总是有相同的环境来测试我的脚本。这就是`./code/ReleaseTheHounds.sh`发挥作用的地方。

这个脚本的存在仅仅是为了帮助我删除一些文件。有时，这可能就像循环遍历一个变量并粉碎任何匹配的文件一样简单。

```
#!/bin/bash
# Set up vars
HOODLUMTYPES="Thief Ruffian Hooligan Delinquent Vandal"
...
# A HOODLUMS
cd /opt/hoodlums/
for i in $HOODLUMTYPES; do shred -n 7 -u -v *$i.zip; done;
...
echo "------------------------------";
echo "LEFTOVER FILES IN /opt/Hoodlums/";
ls /opt/hoodlums/; 
```

Enter fullscreen mode Exit fullscreen mode

我可以在我的 Docker 容器中运行这个脚本，以确保它捕捉到我在`SetUpEnv.sh`中设置的任何边缘情况，而不用担心意外触及我的 MacBook Pro 上的本地`/opt/`目录。

这很好，因为我不想意外核爆 Gradle 或流浪汉！

有时候，我需要先用杀毒软件运行我的数据，然后才能用它来查找文件。

例如，我们的一个输入是`163Killer`，我们测试的文件是`0277_Killer.yaml`。我们需要提取`Killer`来匹配 yaml 文件。这很简单，只需要一个额外的循环和一些 sed 魔法:

```
#!/bin/bash
WHALETYPES="123Fin 456_Gray 495Beluga 385_Livyatan 012_Blue 978Bowhead 149_Humpback 163Killer"
SANITIZEDWHALES=""

function whaleSanitizer {
  input=$1
  input=`echo $input | sed 's/^[0-9]*//'`
  input=`echo $input | sed 's/^_*//'`
  echo $input
}

# B WHALES
for i in $WHALETYPES; do SANITIZEDWHALES="$SANITIZEDWHALES  $(whaleSanitizer $i)"
done;

echo "------------------------------";
echo "LEFTOVER FILES IN /var/lib/docker/";
ls /var/lib/docker/; 
```

Enter fullscreen mode Exit fullscreen mode

没有把它变成一个 Bash post，我们通过`whaleSanitizer`函数运行`WHALETYPES` var，并进入新的`SANITIZEDWHALES` var。然后，我们使用新的 var 来查找需要销毁的文件。

然后，在脚本的结尾，我检查了所有我接触过的目录，以确保我想要删除的文件不再存在。

在我为了分享而简化了一些之后，它变成了一个非常简单的例子。

我正在旋转一个容器，创建一个临时测试环境，然后针对该测试环境运行我的清理脚本。如果它不能正常工作，我可以做一些改变，快速设置我的环境，然后再试一次。

一个额外的好处是，这很容易重复。包含设置和运行测试环境的指令的`README.md`文件只有 176 字节。没错。字节。

当我不可避免地需要更新这个脚本时，关闭 GitHub repo 需要几秒钟的时间。我可以进行更改，在几分钟内就可以建立一个容器并准备好进行测试。更短，如果我已经有了本地提取的`centos:5.11`图像。

* * *

希望这能让容器的好处看起来更真实一点。如果你们中的任何人读到这里，并且不得不为类似的项目(工作或个人)旋转容器，请在评论中提出。让我们进行一些讨论，并帮助新手学习什么时候使用容器！

此外，当我在写 Compose -> Kubernetes 项目/文章时，我也在等待来自亚马逊的一些有趣的玩具。这肯定会是它自己的一篇文章，但我正赶着树莓 Pi Kubernetes 集群的潮流。

[![](img/d65404c1e3b7770ae2d4245faa9385f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--edLWxxpD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cl0eiam0zxfho5c6pa9d.jpg)

> 图片来源:[https://www . C4 labs . com/product/8-slot-stackable-cluster-case-raspberry-pi-3b-and-other-single-board-computers-color-options/](https://www.c4labs.com/product/8-slot-stackable-cluster-case-raspberry-pi-3b-and-other-single-board-computers-color-options/)

在那之前，保持冷静。

[https://Henry needs . coffee](https://henryneeds.coffee)
[博客](https://henryneeds.coffee/blog)
[LinkedIn](https://linkedin.com/in/henryquinniv)
[Twitter](https://twitter.com/quinncuatro)