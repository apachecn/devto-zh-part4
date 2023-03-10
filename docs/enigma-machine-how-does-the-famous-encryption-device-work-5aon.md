# 英格玛机，著名的加密装置是如何工作的？

> 原文：<https://dev.to/maxime1992/enigma-machine-how-does-the-famous-encryption-device-work-5aon>

这篇博客是三篇系列文章的第一篇，叫做**“谜:理解它，实现它，破解它”**:

1.  **英格玛机，著名的加密装置是如何工作的？*【本帖】***

2.  [建立一个只有打字稿的 Enigma 机器，然后使用 Angular DI 系统正确实例化它](https://dev.to/maxime1992/building-an-enigma-machine-with-only-typescript-and-then-use-angular-di-system-to-properly-instantiate-it-2e2h)

3.  [使用 web worker API 从 Enigma 暴力破解加密信息](https://dev.to/maxime1992/brute-forcing-an-encrypted-message-from-enigma-using-the-web-worker-api-166b)

# 目录

*   [简介](#intro)
*   [了解机器](#understanding-the-machine)
    *   [高级原则](#high-level-principles)
    *   [转子如何工作](#how-does-a-rotor-work)
    *   给我看看里面！
    *   [字母和索引](#alphabet-and-index)
    *   它们是如何相互作用的？
    *   [转子如何以及何时旋转？](#how-and-when-do-the-rotors-spin)
    *   自旋如何影响一封信的路径？
    *   什么是反射器？
    *   [完整路径](#complete-path)
*   [结论](#conclusion)
*   挑战:实现你自己的谜！

如果您发现任何打字错误，请在此自行编辑:[https://github . com/maxime 1992/my-dev . to/blob/master/blog-posts/enigma-part-1/enigma-part-1 . MD](https://github.com/maxime1992/my-dev.to/blob/master/blog-posts/enigma-part-1/enigma-part-1.md)并提交一个拉取请求👌

# [T1】简介](#intro)

你听说过**谜**吗？

也许如果我提到电影《[模仿游戏](https://www.imdb.com/title/tt2084970/)》的话，有没有什么印象🔔？

Enigma 是一种加密设备，建于 1918 年，就在第一次世界大战之后。在第二次世界大战期间，德国人开始在战场上使用无线通信系统(使用无线电和莫尔斯)，因为电线很脆弱，可能会被切断，很难移动。它还提供了一个重要的战略优势，例如，你可以在陆地上用船进行交流。只有一个条件:无论谁收听，都可以收听广播。这并不是你真正想保持的优势...

他们决定使用 Enigma 发送加密信息，而不是广播普通信息。

[![Enigma message](img/e3fac96af38f4792e300b5173aeb0ad4.png "Enigma message")](https://res.cloudinary.com/practicaldev/image/fetch/s--zM_Zpz_G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/enigma-message.png)

*用恩尼格玛信用加密的消息[https://enigma.hoerenberg.com/index.php](https://enigma.hoerenberg.com/index.php)T3】*

几周前，我去了布莱奇利公园(从伦敦坐火车不到一个小时)。在这里，为了破解恩尼格玛密码，人们秘密地付出了很多努力。在某个时候，**每天有 9000 人**在这里工作，尽管整个建筑群很小，但任务的重要性是至高无上的。

我真的被那里所有聪明人的工作震惊了，整个组织，当然还有“炸弹”。

[![Bombe machine](img/67df023b00af43381333295c00b8bbfc.png "Bombe machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--t8lYRmJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/bombe-machine.jpg)

*Bombe 机，用来破解用 Enigma 写的加密信息*

为了更好地理解 Enigma 是如何工作的以及幕后发生了什么，我决定实现该机器的一个打字版本。但是在我们深入研究本系列第二部分中的代码之前，我们必须首先真正理解这个机制。

# 了解机器

恩尼格玛机有很多版本。但是今天，我们将关注第一个版本。为什么？

*   我们想了解这台机器的主要概念
*   我们不想迷失在太多的细节中
*   较新的英格玛机器是在那台机器的基础上实现的，核心部分保持不变
*   已经很(好玩了！)理解并实施

一台机器看起来如下:

[![Enigma machine](img/ae7a38d4c1a2c66ebe4bb3baacaf1908.png "Enigma machine")](https://res.cloudinary.com/practicaldev/image/fetch/s---6K_Jya_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/enigma-machine.jpg)

*图像功劳[https://www.flickr.com/photos/timg_vancouver/200625463](https://www.flickr.com/photos/timg_vancouver/200625463)T3】*

## 高层次原则

[![Enigma high level principles](img/23c6624e036d8459d86106a1768b7db3.png "Enigma high level principles")](https://res.cloudinary.com/practicaldev/image/fetch/s--2qwhwBZd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/enigma-schema.jpg)

*图片致谢[https://web . Stanford . edu/class/cs 106j/讲义/36-theenigmamachine . pdf](https://web.stanford.edu/class/cs106j/handouts/36-TheEnigmaMachine.pdf)T3】*

*   电池放在机器内部
*   一个电路从电池开始，通过 3 个转子，然后通过一个反射器，再次通过 3 个转子返回，最终点亮其中一个输出字母*(不要担心，这是最难的部分，我们将回到所有这些)*
*   从输入端(键盘)键入一个字母，将触发转子旋转并改变电路，从而改变路径，导致相同输入字母的不同输出

## 转子是如何工作的

### 给我看看里面！

从内部角度来看，转子只是简单地将字母映射到其他字母。

[![Enigma rotor exploded](img/6519c361e236db2b6ad9a0e0d271f04a.png "Enigma rotor exploded")](https://res.cloudinary.com/practicaldev/image/fetch/s---AaSlwLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/rotor-exploded.jpg)

*一个旋翼——图像功劳[http://www.dgp.toronto.edu/~lockwood/enigma/enigma.htm](http://www.dgp.toronto.edu/%7Elockwood/enigma/enigma.htm)T3】*

注意到上面有电线的黄色部分了吗？对于一个字母的每一个输入，它都物理地连接到一个不同的输出**或**同一个输出。(参见顶部周围的粉红色电缆，连接到输入前面的输出- >相同字母)。

该机器的第一个版本仅使用了 3 个转子，但也有 5 个转子。转子内部的连接没有改变，但是可以改变转子的顺序和它们的**首字母**。这些设置每天都在变化，产生了大量不同的加密信息的解决方案。

### 字母和索引

以下是前 3 个转子的重新映射示例:

*   转子 1: `ekmflgdqvzntowyhxuspaibrcj`
*   转子 2: `ajdksiruxblhwtmcqgznpyfvoe`
*   转子 3: `fvpjiaoyedrzxwgctkuqsbnmhl`

你可能会想到的第一件事是，它可以用以下方式表示:

| 输入 R1 | 输出 R1 | 输入 R2 | 输出 R2 | 输入 R3 | 输出 R3 |
| --- | --- | --- | --- | --- | --- |
| A | E | A | A | A | F |
| B | K | B | J | B | V |
| C | M | C | D | C | P |
| D | F | D | K | D | J |
| E | L | E | S | E | 我 |
| F | G | F | 我 | F | A |
| G | D | G | 稀有 | G | O |
| H | Q | H | U | H | Y |
| 我 | V | 我 | X | 我 | E |
| J | Z | J | B | J | D |
| K | 普通 | K | L | K | 稀有 |
| L | T | L | H | L | Z |
| M | O | M | W | M | X |
| 普通 | W | 普通 | T | 普通 | W |
| O | Y | O | M | O | G |
| P | H | P | C | P | C |
| Q | X | Q | Q | Q | T |
| 稀有 | U | 稀有 | G | 稀有 | K |
| S | S | S | Z | S | U |
| T | P | T | 普通 | T | Q |
| U | A | U | P | U | S |
| V | 我 | V | Y | V | B |
| W | B | W | F | W | 普通 |
| X | 稀有 | X | V | X | M |
| Y | C | Y | O | Y | H |
| Z | J | Z | E | Z | L |

但是正确理解上面的意思很重要:

**从转子 1 的角度来看，索引 0 (A)被重新映射到索引 4 (E)** 。

这里有一个可视化的例子，如果我们键入字母 D 作为输入，我们将**到目前为止**(没有反射器)得到字母 E 作为输出:

[![Letter passing through the rotors](img/b764b570376d63d875c22621ef8c3898.png "Letter passing through the rotors")](https://res.cloudinary.com/practicaldev/image/fetch/s--fCOp74tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/letter-through-3-rotors.png)

你注意到在上面的模式中两个转子之间的箭头总是水平的吗？

### 它们是如何相互作用的？

实际上，转子是这样连接的:

[![2 rotors](img/3b8c7203f9ff0e6bba1a6e632a7c49cf.png "2 rotors")](https://res.cloudinary.com/practicaldev/image/fetch/s--8BUx_TyI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/2-rotors.jpg)

*两个转子之间的连接-图片来源[http://www.dgp.toronto.edu/~lockwood/enigma/enigma.htm](http://www.dgp.toronto.edu/%7Elockwood/enigma/enigma.htm)T3】*

重要的是要了解“争夺”发生在转子本身，只有输出的**指数**计数。因为来自第一个转子的**销将总是连接到它正前方的销**，连接到另一个转子。

### 旋翼如何以及何时旋转？

你可以想象 3 个转子，就像时钟上显示的时间一样:

*   每秒钟，秒针会滴答一次
*   每分钟，分针会滴答一次
*   每小时，时针会滴答一次

对于 Enigma，基本上是一样的，但是基数是 26 而不是 60(字母表的长度)。

*   每一个字母，第一个转子将使一个滴答
*   第一个转子每运行 26 个周期，第二个转子将运行一个周期
*   第二个转子每运行 26 个周期，第三个转子将运行一个周期

### 自旋如何影响字母的路径？

当第一个转子旋转时，路径将完全改变。

如果我们第一次输入 D，我们得到:

[![Letter passing through the rotors](img/b764b570376d63d875c22621ef8c3898.png "Letter passing through the rotors")](https://res.cloudinary.com/practicaldev/image/fetch/s--fCOp74tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/letter-through-3-rotors.png)

但如果我们再次输入 D，首先发生的是第一个转子将移动 1 的偏移量。因此，D 的新路径将是:

[![Letter through the rotors with R1 offset 1](img/ec616b34661013150855098054742a15.png "Letter through the rotors with R1 offset 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--PMahIQSh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/letter-through-3-rotors-r1-offset-1.png)

诸如此类。

在上面，有两件重要的事情需要注意:

*   第一个转子确实旋转了 1°的偏移量
*   第一个转子现在可以表示为以 B 开始，以 A 结束
*   键盘上的 D 字母现在位于第一个转子上的 E 字母前面

## 什么是反光板？

在*【高级原理】*的开头，我简单提到了机器有一个**反射器**。

如果你再看这张图片，你可以在左边看到它:

[![Enigma high level principles](img/23c6624e036d8459d86106a1768b7db3.png "Enigma high level principles")](https://res.cloudinary.com/practicaldev/image/fetch/s--2qwhwBZd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/enigma-schema.jpg)

反射器的特性是:

*   它不像转子那样旋转
*   它只有**一边**，这意味着你不能**将一个字母连接到它自己**，它必须连接到一个不同的字母
*   它的所有字母都与另一个字母相连，否则电路就不完整
*   当反射器输出一个字母时，它将通过三个转子一路返回

**注:**第二点是机器真正重要的特性。在 Enigma 上输入一个字母时，不可能得到与输出相同的字母。

## 完成路径

如果我们:

*   以之前的图像为例，第一个转子已经转动过一次
*   假设反射器映射了字母`T <==> Z` (+所有其他的，但只保留我们这里需要的)

当我们键入字母 D 作为输入时，完整路径如下所示:

[![Complete path with reflector](img/3b5e39bd33494ba414d98f8cdbeb41ab.png "Complete path with reflector")](https://res.cloudinary.com/practicaldev/image/fetch/s--kizwU-YV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/enigma-part-1/assets/complete-path-with-reflector.jpg)

# 结论

Enigma 的核心原理并不复杂，但由于所有的可能性、设置和“随机性”,该机器在很长一段时间内被认为是一种安全的设备(并且是不可破解的),在这种情况下，你可以多次键入相同的字母，并获得不同的输出。

我希望你喜欢在这篇博文中学习英格玛。如果有任何不清楚的地方，请在评论中告诉我，因为本系列的下一部分将会看到我们如何用 Typescript 模拟机器的行为。

# 挑战:实现自己的英格玛！

在阅读本系列的第二部分之前，你能想出上述你自己实现 Enigma 的规格吗？；)

语言无关紧要，平台无关紧要(带 UI 的 web、命令行、Android/iOS 应用等)。如果你成功地构建了它，请在开源仓库上分享你的代码，并在评论中告诉我。我会将您的解决方案添加到一个列表中，这样我们就可以很容易地找到并比较不同的方法！散布消息🐙 ...

# 发现错别字？

如果你在这篇博文中发现了一个错别字、一个可以改进的句子或者任何其他应该更新的内容，你可以通过 git 存储库访问它，并发出一个 pull 请求。请不要发表评论，直接前往[https://github.com/maxime1992/my-dev.to](https://github.com/maxime1992/my-dev.to)并打开一个包含您所做更改的新拉动式请求。