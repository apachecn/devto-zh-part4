# 玩火:毁灭性的命令行命令(rm)

> 原文：<https://dev.to/realtoughcandy/playing-with-fire-a-devastating-command-line-command-rm-20p3>

## 我们来说说其中最危险、最强大，甚至可能是最刺激的命令行命令: **rm。**

`rm`是“移除”的简写在其核心，`rm`允许你以无与伦比的精度和速度删除东西。

好的，很好——所以它移除了一些东西。有什么大不了的，对吧？你不能用垃圾桶按钮做同样的事情吗？为什么是的…是的，你可以。然而，让`rm`可怕的是，一旦你运行了那个命令，就没有“撤销”按钮了。

一去不复返了。

当你阅读涉及到`rm`命令的论坛帖子时，久经沙场的开发人员会明智地用睿智、严厉和大胆的警告来打开他们的帖子:**永远不要运行这个命令，除非你确切地知道你在用它做什么。**

[![rm command](img/b8b52db8a8e5f2d9c2672cd87de6a20a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cYWorT8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yr3dym3kk6758v2h7so8.png)

现在，在我开始演示之前，重复一下智者的话，给出我自己明智而严厉的警告:*谨慎使用`rm`，除非你确切地知道你在用它做什么，否则永远不要运行这个命令。*

### 认真地。`rm`应谨慎而清醒地使用。

(对于本演示，我们处于受控环境中；我也明白其中的风险，所以我们继续吧！)

比方说，我想删除一个顽固的应用程序，它坚持要留在我的机器上，因为它是我的操作系统不可或缺的一部分。Chess.app 真的？为什么我不相信你？我不能把你拖到垃圾桶里——为什么？你得走了。

[![Chess app screenshot](img/7d730f7721bdf385000fd60c6df60336.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d3SzFl7z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1yoxh2n57wk65p5l7ods.png)

别担心，`rm`是来帮忙的。

导航到命令行上的应用程序文件夹，然后我们将键入:`sudo rm -rf Chess.app/`

回归尘土。

### 以下是每个命令所代表的含义:

*   超级用户 do(给你读/写/超能力)
*   `rm` =移除
*   `-rf` =两旗相碰求效率，`r` =“递归”、`f` =“武力”

使用`-rf`，我们告诉它删除从该目录开始的所有内容，一直到它的子文件夹，子文件夹，子文件夹，子文件夹，再到*all llll ll ll*直到所有内容都被遗忘(递归)，而不提示确认(强制)。

### 再来一个。

假设我在一个目录中有一堆不再需要的包含单词“dorkasaurus”的文件。我们将键入`rm`，然后传入星号所包含的关键字:`rm *dorkasaurus*`
拜拜，所有你的多卡龙(但不是“多卡龙”)文件。

[![removing files](img/a91ed67f2205be8d0d63934089f61f87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WRUmaBOG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3lrczolagmishun1x64.png)

这个命令比手动删除文件和目录要快得多。可以想象，这个命令和它的各种标志还会搞乱您的系统，毁掉您的一周。

### 记住，`rm`没有撤销按钮。

你可以考虑使用的一个安全的替代工具是像 [trash-cli](https://github.com/sindresorhus/trash-cli) 这样的实用程序，它将你的文件发送到回收站，这样你可以在以后手动删除它们。

此外，如果您想知道每个命令及其选项的作用，您可以使用`man`命令后跟您感兴趣的命令来调用叫做*手册页*的东西。例如:

`man pwd`
`man ls`
T2】

[![manpages](img/304aea545e0d9428ac55dc79dc980046.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S-IZSfaM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/281p2bxbu8ntr1d0v04y.png)

手册页本质上是一个指南，解释了每个命令及其标志的作用。

### 综上所述，`rm`是现存最强大的命令行命令。

不管你是使用“普通 rm”还是与标志
结合使用，比如`-rf`，都应该小心使用，而不是鲁莽放弃。如果你打算用它做实验，考虑使用一个虚拟机或者其他一些可以永远破坏东西的计算机。

黑客快乐！

附言:在 YouTube 上关注我，我在那里谈论了很多很酷的网络开发的东西:

[![](img/429df5f215c75f692495b511a25b8170.png)](http://www.youtube.com/watch?feature=player_embedded&v=AezH5ef1_3U<br>%0A)