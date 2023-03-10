# 为什么浮点数如此奇怪

> 原文：<https://dev.to/alldanielscott/why-floating-point-numbers-are-so-weird-e03>

如果您以前编写过任何 JavaScript(内部使用浮点数)，或者您在其他语言中处理过双精度或单精度浮点数，那么您可能会遇到这样的版本:

`return (0.1 + 0.2 == 0.3); // Returns FALSE !!!
... and the walls in your office float away as the laws of mathematics begin to crumble`

或者，也许你已经对几个看起来合理的数字(有一两位小数)做了一些加法或减法，然后将结果打印到屏幕上，当你期望一个更合理的 10.7 时，却遇到了类似 10.66666666666669 的结果。

如果你没有经历过整个大学的学习过程，也没有从头到尾解释过浮动，那么你可能会有一两个“WTF”时刻。这是正在发生的事情的概要...

## “浮点”中的浮点是什么意思

简而言之，浮点数以一种科学记数法的形式存储在内存中，这种记数法允许有限数量的“有效数字”和有限的“小数位数”。科学记数法是这样的(记得回到高中):

1200，000，000，000，000，000，000 = 1.2 倍 10^21

那个数有两个有效数字(1，和 2)，构成了“尾数”(或者说是数的“肉”)。“12”之后的所有零都是由以 10 为底的指数创建的，它只是将小数点向右移动一些位置。指数可以添加许多零(以非常低的存储成本)，但它不能容纳任何“肉”。

负指数可以用来将小数点向左移动，得到一个非常小的数字。

0.000，000，000，000，000，001，2 = 1.2 x 10^-21

## 最重要的是精准

假设我们有一个数据类型，它可以接受 2 个有效(十进制)数字，并且允许(十进制)指数达到+/-21。上面的两个示例数字将接近我可以用该数据类型表示的最大值和最小值(最大值和最小值实际上分别是 9.9x10^21 和 0.1x10^-21)。

接下来，如果我试图用这种虚构的两位数精度浮点数据类型保存数字 121000000000000000，那么我就会像他们说的那样是 s . o . l . t1，结果会是 12000000000000000000，因为我的两位数精度不允许 1.21 x

这是浮点数的所谓“精度损失”错误的一个来源。

## 循环分数

精度损失的另一个来源(占 0.1 + 0.2！= 0.3 喜笑颜开)是由于什么能和什么不能用一个以 2 为基数的数字系统精确表示。

这和十进制数字系统对于三分之一(0.3333333333333333333333333333333333333333333333333)这样的数字存在同样的问题...有人吗？).

计算机不将数字存储为十进制，所以计算机中浮点数内部的所有内容都是使用以 2 为基数的数字系统存储的。

只要把上面例子中所有的 x10^n 引用替换成 x2^n，你就会发现一些十进制数(以 10 为基数)非常合适，而其他的就不合适了。对于你我来说，0.1 可能是一个很容易处理的数字(作为十进制生物)，但对于两指二进制数豆器来说，它就像十进制中的 1/3 或 3/7 一样笨拙。

## 有点啰嗦的趣味来说明

### 问题:循环分数

重新创建那个(二进制)0.1 + 0.2！= 0.3 十进制问题，假设我们为某个神话般的基于十进制的计算机编写一个程序，使用一个可以存储 4 个有效十进制数字的数值数据类型。现在让我们试着让程序算出 1/3 + 2/3 是否等于 1。

我们开始吧:

1.  **陈述:**存储这个数字:1/3—*在这个例子中，我们要说的是人类操作员不理解十进制系统，只处理分数。十进制是给计算机用的:真正的男人用分数！*
2.  **动作:**存储. 3333 — *当你用十进制数字在代码中声明一个数字时，或者你接受十进制用户输入，并把它作为二进制浮点数放入内存时，就会发生这种情况*
3.  **语句:**存储此号码:2/3
4.  **行动**店铺. 6666
5.  语句:将这两个数相加
6.  **动作:**计算. 9999

现在，让我们试着从我们输入的内容中获得一些意义:

1.  **问题:**总计(. 9999)等于 1.000 吗？**
2.  **回答:**见鬼没有！(错误)
3.  **程序员** : *扯下几根头发，大声说“WTF？1/3 加 2/3 肯定等于 1！这台计算机坏了！”*

### 解

解决这种缺乏精确性的方法是停止试图精确比较不能(也不应该)精确比较的东西。相反，我们必须决定我们需要两个事物有多接近，才能认为它们对我们的目的是“相等的”。

以下是计算机伪说话的正确解决方法:

1.  **问题:**. 9999 是否足够接近 _ 1.000？
2.  **错误:未定义常数:** WTF？你*一直在抽什么？多近才算够近？*

哎呀！让我们再试一次:

1.  **声明:**足够接近(我选择的公差)是正负. 1000
2.  **问题:**. 9999 是否足够接近 _ 1.000？
3.  **回答:**是(真)——*0.9999 和 1.000 的区别是 0.0001:那真他妈的近，比足够近还要近 _ T3】*

所以你可以看到，如果三分对人类(作为一个物种)真的很重要，那么我们可能会使用基数为 3 或基数为 9 的数字系统，因为用十进制(和二进制)处理它们是不方便的！

此外，因为这些是循环分数，所以我们是否能保存 4 个有效数字或 4000 个有效数字并不重要:1/3 + 2/3 在输入我们的“决策计算机”时永远不会精确地等于 1。我们总是需要允许一些公差，内置的等式运算符将总是(准确地)反映(0.3333...+ 0.6666...！= 1).

## 将我们的例子扩展到其他浮点怪癖

如果你观察力非常敏锐，你可能会注意到——在前面的例子中——1.000 这个数字只有三位小数，而. 9999 这个数字却有四位。我们这里假设的“十进制存储类型”只支持 4 个有效数字，所以如果我们试图在“1”的位置存储一个数字，我们不知道第四个小数位置可能是什么。

如果您尝试将 4，123，134 与 4，123，000 进行比较，您可能会想到这种假想的 4 位数浮点类型可能会出现的一些问题。我们只有 4 个有效数字，所以这两个数字将分别变成 4.123 x 10^3 和 4.123 x 10^3——同一个数字！

如果您开始尝试以双精度浮点类型存储大整数，那么在某个时候(大于 9，007，199，254，740，991)，您将开始遇到这个问题。对于单精度浮点数，它的数量要少得多。

类似地，如果你试图处理不同标度的数字，你也会遇到问题(试着用我们假设的 4 位有效数字数据类型从 4356 中减去. 0001！).

## 阅读更多

所以，现在你知道原因了，你不一定会被困在要么做要么死的唯一选择中:有变通办法！

本系列的另一篇文章讨论了如何在程序中选择一个合理的容差来比较浮点数(以及何时最好完全避免使用它们)。

尽管它是用 JavaScript 编写的，但同样的准则适用于所有浮点类型的语言。

[如何在 JavaScript 中正确比较数字](https://dev.to/alldanielscott/how-to-compare-numbers-correctly-in-javascript-1l4i)