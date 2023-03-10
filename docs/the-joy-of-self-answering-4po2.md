# 自我回答的快乐

> 原文：<https://dev.to/jericson/the-joy-of-self-answering-4po2>

当栈溢出开始的时候，对我来说没有太多的内容。大多数人似乎有兴趣询问一些不知名的微软产品，比如 C#和。网。我在一家 UNIX 商店工作，我们使用了可靠的技术，比如 T2 C 和 Perl T3。但是我对一个专门提供问题和答案的网站的想法很感兴趣，所以我想我应该试着问一些问题。

顺便说一下，栈溢出引起了我的兴趣，因为我在 21 世纪初读过乔恩·尤德尔写的 [*实用互联网群件*](http://shop.oreilly.com/product/9781565925373.do) 。(或者可能读了《T4》关于《T5》的书，足以对这个概念感到兴奋。)这本书的前提是，互联网将从根本上改变人们相互交往的方式，因为它允许群体进行大规模合作。很难解释这个想法有多令人兴奋，因为我们现在认为它显然是正确的。但是在推特、脸书、播客、博客甚至论坛出现之前，很少有人想到互联网会让公众话语民主化。

堆栈溢出解决了一个非常具体的问题:对晦涩难懂的编程知识的不均衡访问。我很难回忆起来，但曾经有一段时间，每个程序员都不得不发现自己的奇怪之处，例如:

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png)[T2】](https://stackoverflow.com/questions/583889/how-can-you-untar-more-than-one-file-at-a-time)

Feb 24 '09 Comments: Answers: 3[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)76![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/583889/how-can-you-untar-more-than-one-file-at-a-time) </header>

我在一个目录中有一堆`tar`文件，我想一次提取其中的所有文件。但这似乎没有任何作用:

```
$ tar xf *.tar 
```

这是怎么回事？如何一次解压一堆文件？

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/583889/how-can-you-untar-more-than-one-file-at-a-time)</button>

然后，您需要在办公室里走一走，看看是否有人在 shell 命令方面足够熟练，能够找到一个可行的解决方案。或者，只是通过手动解开数百个文件的斗争。(问我怎么知道这个的。)但是有了堆栈溢出，就可以有人出现，看到问题并提供答案:

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ 回答回复:如何一次解压多个文件？](https://stackoverflow.com/questions/583889/how-can-you-untar-more-than-one-file-at-a-time/583891#583891)

Feb 24 '09[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)147![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/583889/how-can-you-untar-more-than-one-file-at-a-time/583891#583891) </header>

# 这是怎么回事？

最初，`tar`命令旨在用于磁带设备。因为一次只在一个设备上执行`tar`是有意义的，所以语法被设计为假设一个且只有一个设备。假定传递的第一个文件或目录是…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/583889/how-can-you-untar-more-than-one-file-at-a-time/583891#583891)</button>

然后大约 68，000 人可以找到完全相同的答案，并了解一些关于 UNIX shell 如何工作的知识。

关键在于:提问和回答的人不一定是不同的。我找到了自己的解决方案，而不是继续我的生活，我贴出了我自己问题的答案。也许这不是世界上最伟大的事情，但这是我将来帮助人们的一点小小的方式。

现在，你很难像不知道答案一样问一个问题，而且网站上有人对自己回答的问题持怀疑态度。但是如果你希望互联网上有一些编程知识，你不需要等别人来问这个问题。