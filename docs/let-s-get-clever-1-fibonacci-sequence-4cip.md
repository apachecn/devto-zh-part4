# 让我们变聪明#1:斐波纳契数列

> 原文：<https://dev.to/codemouse92/let-s-get-clever-1-fibonacci-sequence-4cip>

我们花了很多时间试图写出好的代码。我不知道你怎么想，但我已经厌倦了听到“不要自作聪明”的告诫。

每个人都需要休息。所以，为了庆祝我们所有不同的技能和知识， ***让我们变聪明吧！*T3】**

# 挑战#1

**计算斐波那契数列的第 n 位数字。**

在斐波纳契数列中，每个元素都是前两项的和。

```
0, 1, 1, 2, 3, 5, 8... 
```

Enter fullscreen mode Exit fullscreen mode

所以，下一个值是`5 + 8`，或者说`13`。

您的代码应该能够找到第`n`个元素。

## 约束

*   不允许硬编码！您不能在代码中的任何地方手动写出序列。每个元素(除了首字母`0, 1, 1`)都必须由代码生成。

*   `n=0`是`0`；`n=1`和`n=2`都是`1`

*   假设`0 <= n < (2^16 − 1)`。换句话说，不用担心负数，假设`n`小于一个 8 位整数的最大可能值。(没错，以前是 64 位整数...我更新了规格以保持理智。)

## 奖励积分

这些不是必须的，但是如果你实现了这些目标中的任何一个，你就有了炫耀的权利！

*   🔂没有循环:你能避免使用任何显式循环或递归吗？

*   ⏱️ **效率:**能打得过`O(n)`吗？

# 规则

(这些对于每一个“让我们变得更聪明”的挑战都是一样的。)

1.  你聪明的代码应该以你喜欢的任何方式完成既定的目标。不过，要小心边缘情况。

2.  你可以使用任何你喜欢的语言。然而，我不鼓励使用刻意深奥的语言。在生产语言中，聪明通常更有趣。

3.  你知道所有关于可读性和可维护性的规则吗？ *Fuhgeddaboudit。*聪明是目标。

4.  便携性，内存安全，安全性？*呸骗子。* [梅尔](http://www.catb.org/jargon/html/story-of-mel.html)表示欢迎。

5.  **效率是*而不是*主要目标**，但是如果你的聪明恰好有值得夸耀的效率，一定要指出来！

6.  解释你的聪明之处。不要等别人问你，虽然你要做好回答问题的准备。聪明代码的巧妙副作用是每个人都可以学到很酷很奇怪的东西。

7.  对反馈持开放态度。其他人可能会发现你错过的边缘案例，或者有想法让你的聪明方法甚至*更加*聪明。

8.  打扮是允许的，但是不要对此感到厌恶，同样也不要吝惜对他人的赞美。把虚伪的谦虚留在抽屉里。(冒名顶替综合征太烂了，让我们用这个狠狠踢它一脚。)

9.  投聪明一票！如果你真的喜欢某人的解决方案，一定要给它一个赞。

10.  始终保持礼貌和建设性。请不要批评任何人不够聪明，不明白自己做了什么，或者问了一个“愚蠢的问题”。这是聪明的表现。做导游！

别忘了: ***鼓励代码黑客攻击！*** 无所顾忌。