# 我做了一个 xargs！

> 原文：<https://dev.to/jacoby/i-did-an-xargs-5096>

你必须知道的一个基本事实是没有一套规范的知识...实际上，几乎任何事情都可以，但在这里，对于 Linux/Unix 专业知识。有研究表明。我们在知识上都有差距。我确信有人对文件系统表示的底层链表有丰富的知识，但是不知道`ls -lh`以人类可读的形式给出文件大小。(如果是你，不客气。)

对我来说，*我长期以来的知识缺口之一是`xargs`。我已经看了几十年了，所有的`find . | xargs <something>`我已经复制并粘贴了那些命令，但知识从未停留。*

## 继续

在过去的一个半月里，我们使用的一个 API 一直没有工作，这意味着我们无法更新数据。但是那个错误已经被修复了，现在我已经收到了几百个被要求的更新。

我有一个 API 工具，它允许我从 API 的中获取整整一个月的数据*，并放入列中，这一次我记得`awk`是我想要的数据(`sed`和`awk`是我 Unix 知识中的另外两个空白，但那是另一个列)，让我:* 

```
1sf701vdlahb
3lm63b58riej
3o4rhtaxe8lx
b5hqvs6tuqnm
eoirmijclnl1
uihc1q3yzkvm
vq2uskrrlb5h
w6ulq3sdvyir
wxk3bbdpwdx3
y2npmn6yjkcx
zepz3n2e2to7
zk08o7dysokt
... 
```

(不，那只是随机的字符串，但是请原谅我。)

因此，我需要为所有这些运行`api_tool -status done -r`，并考虑所有其他选项的痛苦。id 列表放到一个文件中，然后...bash `for`循环？给每一行加上`api_tool ...`和`sh id_list.txt`？

然后我想起了`xargs`，半打标签被打开。

`xargs`有很多*的*旗帜，所以比我在这里用的要多得多，但我用的是`api_tool -q not_done -f 2019-06-01 | awk "{print $1}" | xargs -n 1 api_tool -status done -r`。

*这个* `awk`命令第一次就对了(耶我！)但是我对 echo 运行了几次`xargs`才得到正确的结果。`-n <number>`确定它一次从条目列表中取出的条目数量(我们只想要一个)并将其附加到列表的末尾，因此`<whatever> | xargs -n 1 <command>`变成了`<command> <first entry>`，以此类推。

这省去了我一整 *很多*无意义的打字，我很高兴。

## 寓意

重点是你要*在有差距的时候认出*，*利用*机会学习东西，*记录*这些东西，以便你(和其他人)以后用得上。

而且，`xargs`很酷，哟。