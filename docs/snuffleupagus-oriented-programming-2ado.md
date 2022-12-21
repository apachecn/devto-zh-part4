# 面向 Snuffleupagus 的程序设计

> 原文：<https://dev.to/csmacnz/snuffleupagus-oriented-programming-2ado>

> [![Snuffleupagus-Oriented Programming](img/62a9dec7ec700d6f13ec84f83060e0a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--luVRidSr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.csmac.nz/conteimg/2019/07/1rYH-1-.gif)
> 
> 面向 Snuffleupagus 编程(SOP)的即兴介绍:[https://t.co/jPkjPRgqQr](https://t.co/jPkjPRgqQr)
> 
> —凯西·穆拉托里(@ cmuratori)[2015 年 7 月 21 日](https://twitter.com/cmuratori/status/623332420855541764?ref_src=twsrc%5Etfw)

有时候，说出某件事是开始谈论一个话题的最佳方式，很多人都这样做了，但没有意识到或没有形成一个概念。这是那些最终有好(？)名。

如何构建一个新的 API？这里我们指的是有效地编程契约。你可以一直黑下去，直到你有了实用的东西，然后开始消费它。更好的是，把它构建成完成某项工作的代码，然后交付给其他人尝试和使用。

大多数开发人员都看到了跨团队边界、层边界、可重用组件、HTTP/Soap 边界等提前进行契约设计的价值。一个进化的设计永远不会比一个有意的设计好。另一方面，我们都在各种语言中使用过笨重的库 API，无论是 BCL 还是其他语言。

### 什么是 Snuffleupagus 先生

这个名字来自芝麻街。基本上是布偶。大鸟和伯特和厄尼，埃尔莫等。几十年前有一个故事情节，大鸟一直看到 Snuffleupagus 先生并谈论他，但没有其他人见过他。他们断定它是一种虚构的生物已经很久了。在节目中，真相是什么是非常模糊的。最终，他们实际上也看到了 Snuffleupagus，并得出结论，它毕竟不是虚构的。[更多在此。](https://www.smithsonianmag.com/smart-news/brief-history-sesame-streets-snuffleupagus-iidentity-crisis-180957351/)

### 输入 SOP

这个故事的思想与构建 API 的方法相匹配。

首先，我们要想象我们想要使用的东西是存在的。然后，我们将针对 API 编写代码，在不实现任何东西的情况下，创建我们的用例。一旦我们对我们的 API 是可用的感到高兴，我们就实现它并使它实际存在，使它成为现实。一旦考虑到实现的现实和限制，可能会有一些调整，但想法是尽可能保持它与原始用例一样可用，可用性完好无损。

[通过送礼者](https://gifer.com)

### TDD

这也是使用 TDD 的一个很好的候选方法，只是需要更多的实现代码来通过测试。像 Snuffleupagus 先生一样，这个实现代码是虚构的，直到你有足够的测试用例来使它成为现实，并进行重构。

### 感谢

往前走，开始分享面向 SOP、Snuffleupagus 的编程。想象，建立用法，让它成为现实。感谢凯西·穆拉托里创造了这个术语并使其得以坚持。在[手工英雄](https://handmadehero.org/) ( [YouTube](https://www.youtube.com/handmadehero) )和他的游戏公司 [Molly Rocket](https://mollyrocket.com/nexus) 上查看他的更多编程。