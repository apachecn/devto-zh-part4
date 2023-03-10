# 理解 RSA 加密背后的数论

> 原文：<https://dev.to/renegadecoder94/understanding-the-number-theory-behind-rsa-encryption-1pdo>

几个月后我就要参加资格考试了，我想我可以记录下我将要学习的一些东西。例如，作为算法课程的一部分，我学习了 RSA 加密。不幸的是，算法本身就相当复杂，但我也有责任理解它背后的数论。是时候复习我的模运算了！

## RSA 加密概述

在我们进入细节之前，我想我们可以从什么是 RSA 加密以及它如何在高层次上工作开始。

RSA 加密(来自 Rivest、Shamir 和 Adelman 的名字)是一种加密方法，它依靠陷门单向函数来生成一对用于数据加密的密钥。一个密钥被称为私钥，它被隐藏起来供个人使用。同时，另一个密钥被称为公钥，它被分发给任何可能使用它的人。

一前一后，这些密钥被用来在个人之间交换加密信息。例如，如果我想给你发一条消息，我会用你的公钥加密。要读取它，你可以用你的私钥解密它。自然，一旦消息用公钥加密，只有私钥可以用来解密，反之亦然。

在 RSA 的情况下，用于生成密钥的单向函数是从质因数分解的困难中得到的，质因数分解是将一个数分解成它的质因数的能力。换句话说，RSA 加密确保了很容易生成一对密钥，但是在给定一个密钥的情况下，很难找出另一个密钥。

无论如何，在接下来的部分中，我将介绍 RSA 加密背后的一些数论，并且我将介绍实际的 RSA 加密算法。这些内容很多都是从俄亥俄州立大学的 CSE 6331 课堂笔记中借来的，但所有的分析都是我自己的。

## 数论背景

为了理解 RSA 加密背后的算法，有一点数论背景知识是有帮助的。

### 模运算

大部分 RSA 加密都是建立在模运算的基础上的，模运算使用一个由整数组成的数系，这些整数在某个极限处回绕。对于大多数开发人员来说，模运算实际上是第二天性，因为大多数编程语言中的整数都有限制。然而，这个概念背后的数学要复杂得多。

在我们开始有趣的事情之前，让我们先来讨论一下模操作符。在许多语言中，模运算符是百分号(%)。大多数古老的语言没有真正的模运算符。相反，它们有一个余数运算符。差别很细微，但却很重要。

对于真模运算符，模数定义了可以循环的值的范围。例如，`13 % 5`将是 3，因为 13 在确定为 3 之前会围绕 5 循环两次，就像只有五个滴答(1，2，3，4，5 (0))的时钟一样。巧合的是，13 除以 5 也有一个余数 3，所以运算混淆也就不足为奇了。

也就是说，当引入负数时，事情变得有趣了。例如，`13 % -5`将是-2，而余数仍然是 3。在模的情况下，我们定义了一个新的循环，它只包含负值(-5 (0)，-4，-3，-2，-1)。

出于我自己的理智，我实际上用 Python(真实模式)和 Java(剩余模式)测试了其中的一些。结果如下:

```
>>> 17 % 4
1
>>> 17 % -4
-3
>>> -17 % 4
3
>>> -17 % -4
-1 
```

Enter fullscreen mode Exit fullscreen mode

```
> 17 % 4
1
> 17 % -4
1
> -17 % 4
-1
> -17 % -4
-1 
```

Enter fullscreen mode Exit fullscreen mode

通过模运算，我们得到四个不同的答案——每个周期方向和方向一个。与此同时，余数只给我们两个——每个股息一个。

### 同余

虽然模运算本身并不那么有趣，但它有一些有趣的特性。特别是，我们可以开始讨论同余。为了做到这一点，我们可能应该涵盖一个新的公约，所谓的分歧。

在数学中，我们表明`a`用一个新符号`|`来除`b`。例如，`3|9`表示 3 除以 9。虽然它是一个简单的符号，但我们可以用它来定义同余。

现在，`a`全等(≡)到`b mod n`如果`n|(a-b)`。换句话说，`a`和`b`除以`n`的“余数”相同。例如，`121 ≡ 16 mod 7`因为这两个值除以 7 的余数都是 2。

有了这种同余关系，我们就能够得出模运算的一些非常有趣的性质。例如，`a ≡ a mod n`因为`a`和`a`被`n`除后有相同的“余数”。我们称之为自反性质。

除了自反性质，还有对称性质，即`a ≡ b mod n`等价于`b ≡ a mod n`。最后，我们有传递性，即如果`a ≡ b mod n`和`b ≡ c mod n`那么`a ≡ c mod n`。

这三个性质一起证明了模 n 同余是一种等价关系。然后，我们可以使用这个关系开始将全等的值分组为集合:[a] <sub>n</sub> = {x ∈ Z : x ≡ a mod n}。例如，模 2 产生两组数字:偶数([0] <sub>2</sub> 和奇数([1] <sub>2</sub> )。这些集合被称为[剩余类](https://artofproblemsolving.com/wiki/index.php/Residue_class)，其中剩余可以被认为是余数的另一个单词。

### 组

不幸的是，在我们真正深入研究加密算法之前，仍然有相当多的数论要研究。例如，探索群体的概念是很重要的。

在高层次上，组(`G`)是一个集合，其中可以使用二元运算符(`*`)将两个元素组合成第三个元素。然而，这三个元素之间的关系必须遵循四个条件:闭包、结合律、恒等式和逆矩阵。

*   闭包:`∀x,y ∈ G, x*y ∈ G`(换句话说，对于 G 中的所有 x 和 y，x * y 的结果也在 G 中)
*   关联性:`x*(y*z) = (x*y)*z`
*   等式:`∃e ∈ G s.t. ∀x ∈ G, e*x = x*e = x`(换句话说，G 中存在一个元素 e，使得对于每个元素 x，等式成立)
*   逆:`∀x ∈ G, ∃y ∈ G s.t. x * y = y * x = e`(换句话说，对于 G 中的每个 x，G 中存在一个 y，使得在元素之间执行二元运算产生单位元素)

组的一个例子是所有整数和加法运算符 or (Z，+)的集合。同样，对于所有有理数的集合，(Q，+)和所有实数的集合，(R，+)也是如此。

### 残类组

有了我们对剩余类和群的了解，我们可以开始定义剩余类的群，就像加法所限定的群，(Z <sub>n</sub> ，+)。具体来说，Z <sub>n</sub> 定义为包含所有剩余类模 n(即`{[0], [1], [2], ..., [n - 1]}`)的集合。

然而，首先我们应该为剩余类定义一些运算。事实证明，剩余类有一个简单的性质:它们可以直接相加和相乘。特别是，如果`x ∈ [a], y ∈ [b]`，那么`x + y ∈ [a + b]`和`x ⋅ y ∈ [a ⋅ b]`。

现在，加法是否足以构成一个群？事实证明，是的。毕竟，它检查所有四个框:

*   闭包:整数加法已经通过了这个标准
*   结合性:同上！
*   恒等式:和整数加法一样，0 是我们的恒等式元素
*   倒数:同样，整数加法将-a 定义为倒数

可惜，(Z <sub>n</sub> ，⋅)不是一个组，因为 0 <sup>-1</sup> 不存在。更糟糕的是，即使当整数集合是严格正的时候，一些逆也不存在。特别地，我们定义逆使得对于 a ∈ Z <sub>n</sub> ，a <sup>-1</sup> 存在当且仅当 gcd(a，n) = 1，其中 gcd 是最大公约数。换句话说，a 一定是 n 的互素。

自然，下一步将是定义一个新的剩余类集合，它只包含 n 的相对素数。我们称这个集合 Z<sub>n</sub>T2】*，它定义如下:{a ∈ Z <sub>n</sub> : gcd(a，n) = 1}。这个集合的一个例子是 Z <sub>12</sub> <sup>*</sup> ，它包含 1、5、7 和 11——0 和 11 之间 n 的所有相对素数。

现在，这个新集合是否足以用乘法组成一个群？再一次，是的！特别是，结合律和恒等式遵循乘法运算。此外，我们可以使用扩展的欧几里德算法计算逆。这是我觉得最有趣的闭包性质。不知何故，一个⋅ b (mod n)总是在集合中。

### 基数

接下来，集合和组的一个有趣的属性是基数:元素的大小或数量(|S|其中 S 是某个集合)。对于一组典型的模 n 剩余类，我们正好有 n 个元素。我们如何着手测量我们的相对素剩余类集的基数？

事实证明，我们的集合 Z <sub>n</sub> <sup>*</sup> 的基数可以用欧拉的 t 整数函数来度量。该功能有两种使用方式:

1.  *φ*(p<sup>e</sup>)=(p–1)p<sup>(e-1)</sup>对于素数 p
2.  *φ*(ab)=*φ*(a)*φ*(b)如果 gcd(a，b) = 1

知道了这个事实，我们实际上可以计算 Z 中任意 n 的基数 <sub>n</sub> <sup>*</sup> 。比如说，假设 n 是 15。那么，*φ*(15)=*φ*(5)*φ*(3)= 4⋅2 = 8。现在，让我们看看是否能把它们都列出来:{1，2，4，7，8，11，13，14}。

有趣的是，知道 Z <sub>n</sub> <sup>*</sup> 的基数后，会产生一些性质。例如， [Langrange 定理](https://crypto.stanford.edu/pbc/notes/group/lagrange.html)表明，对于我们群中的任意一个 a，a 的那个群的基数的幂等于那个群的单位元(a ∈ G，a <sup>|G|</sup> = e)。从上面的例子来看:

> 2<sup>8</sup>对 15 = 1。

作为该性质的推论，a 的某个 m 的幂等于 a 的 m 的幂模 G 的基数(a ∈ G，a <sup>m</sup> = a <sup>m mod |G|</sup> )。再次使用上面的例子:

> 2<sup>57</sup>= 2<sup>57 mod 8</sup>= 2<sup>1</sup>= 2。

在此之上，欧拉定理指出，对于 Z <sub>n</sub> <sup>*</sup> 中的任意一个 a，a 对 Z <sub>n</sub> <sup>*</sup> 的基数的幂为 1(a∈Z<sub>n<sup>*</sup>，a <sup>*φ* (n)</sup> = 1)。再次使用上面的例子:</sub>

> 7 <sup>8</sup> 对 15 = 1。

最后费马小定理指出，如果 a 在 Z <sub>p</sub> <sup>*</sup> 其中 p 是素数，那么 a 到 Z <sub>p</sub> <sup>*</sup> 的基数等于 a 到 p 减 1 或 1 的幂(a∈Z<sub>p</sub>T10】*，a<sup>T13】φ(p)</sup>= a<sup>p-1</sup>= a 使用一个新的例子，其中 p 是 11:

> 7 <sup>φ(11)</sup> = 7 <sup>10</sup> = 1。

## RSA 加密算法

鉴于我们在数论方面的新背景，RSA 加密算法应该非常简单。

### 第一步:选择大素数

首先，我们要做的第一件事是挑选两个非常大的素数(> = 2048 位)。我们想这样做是因为质因数分解是一项非常困难的任务。虽然将两个质数相乘得到一个合成值非常容易，但是要找出哪两个质数产生了这个合成值(也就是一个单向函数)要困难得多。

不幸的是，寻找大质数不是一件小事。为此，我们通常使用某种形式的猜测和检查方法。换句话说，我们生成一些我们想要的长度的大数，并测试它是否是质数。

为了测试一个值是否是质数，我们可以用这个值除以 2 和它的平方根之间的所有数。当然，对于我们想要测试的大值类型，这个过程很慢，所以如果有更好的方法就好了。

输入:费马测试。之前我们陈述过，如果 n 是质数，那么对于任意 a: a <sup>n-1</sup> = 1 (mod n)。换句话说，在 1 和 n-1 之间随机选择一个 a，然后求解方程。如果结果是 1，我们可能有一个素数。然而，有一些复合值通过了这个测试，它们被称为卡迈克尔数。

为了改进费马试验，米勒-拉宾试验诞生了。除了计算费马检验，米勒-拉宾检验增加了一个额外的概率检验，进一步排除了卡迈克尔数。将来，我可能会更深入地研究这个算法。

无论如何，有了这两个质数(p 和 q ),我们将会计算 n 为 p 和 q 的乘积。

### 第二步:计算加密密钥

从 n 中，我们需要选择第一个**e**n 加密密钥， **e** 。我们通过在 1 和基数 Z <sub>n</sub> <sup>*</sup> (又名 *φ* (n))之间选择一个值来实现这一点。作为附加准则，e 必须与 *φ* (n)互质。

举个例子，考虑两个小素数:13 和 23。在这种情况下，n 是 p ⋅ q = 13 ⋅ 23 = 299。从那里， *φ* (n)计算起来很简单:(p–1)(q–1)=(12)(22)= 264。换句话说，我们需要在 1 和 264 之间选择一个 e，它也是 264 的互质。为了简单起见，我们将选择一些不会被 264 整除的小素数。19 怎么样？

有了 e，我们就可以计算出 **d** 解密密钥， **d** ，如下:d = e <sup>-1</sup> mod *φ* (n)。换句话说，ed ≡ 1 mod *φ* (n)。为此，我们可以使用欧几里德的扩展算法，但为了简单起见，让我们使用这个[模乘逆](https://planetcalc.com/3311/)计算器。在这种情况下，d = 139。

### 第三步:盈利

有了这两个常数，我们就可以开始加密和解密消息了。为此，我们需要分发我们的公钥。按照惯例，我们将 d 与 n (139，299)结合使用作为我们的私钥，e 与 n (19，299)结合使用作为我们的公钥。

从那里，如果有人想给我们发送加密信息，他们会用我们的公钥加密他们的信息。为了简单起见，他们可能会将消息中的每个字符转换成它的 ASCII 值(m)。然后，他们将公钥应用于该值，如下所示:c = m <sup>e</sup> mod n。当我们解密该值时，我们将使用相同的函数，除了 e 将被 d 代替(因为它们是逆的):m = c <sup>d</sup> mod n

因为我们沿着 e 分布 n，我们必须确保 n 足够大。否则，有人可以很容易地将 n 降低到它的质因数并计算出 d，目前，行业标准要求 n 大于 2048 位。

## 想了解更多？

在写这篇文章的过程中，我意识到我不会在上面接受测试，所以我不再花这么多精力。因此，可能有一些概念你仍然想探索，比如素性测试。同样，您可能希望看到更多具体的例子。不幸的是，在这个时候，我不能提供任何额外的支持。

然而，如果有足够的兴趣，我可能会扩展这篇文章或写一篇额外的文章来澄清你想知道的任何事情。作为一个在计算机科学的数学方面苦苦挣扎的人，我不确定我能帮上多少忙，但我总是愿意尝试！

与此同时，通过电子邮件列表订阅[成为会员](https://therenegadecoder.com/members/)或[，帮我让这感觉不那么浪费时间。](https://newsletter.therenegadecoder.com/)

如果您不喜欢书籍，但对与 RSA 加密相关的主题感兴趣，请查阅以下文章:

*   [使用模运算的石头剪刀布](https://dev.to/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)
*   [如何在 Python 中获取列表的最后一项](https://therenegadecoder.com/code/how-to-get-the-last-item-of-a-list-in-python/)

再次感谢您的支持！

理解 RSA 加密背后的数论的帖子最先出现在叛逆的编码者 T2 的文章中。