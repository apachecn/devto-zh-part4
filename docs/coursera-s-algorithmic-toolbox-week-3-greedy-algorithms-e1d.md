# Coursera 的算法工具箱第 3 周-贪婪算法

> 原文：<https://dev.to/avatarkaleb/coursera-s-algorithmic-toolbox-week-3-greedy-algorithms-e1d>

在解决问题时，重要的是要有不同类型的策略来找到最佳解决方案。其中一个策略，贪婪算法，是第三周的主题。

## 什么是贪婪算法？

贪婪算法是我们解决问题的工具箱中的一种算法，它们可以分为四个主要步骤:

1.  做出贪婪的选择
2.  证明选择是安全的选择
3.  归结为一个子问题
4.  重复
5.  优化(如果需要，可选)

*以下步骤的最大数例题*

给定一个一位数的数字列表，从中创建最大可能的数字。

```
Input: 8 7 6 1 3 2 4 5 9
Output: 987654321 
```

### 做出贪婪的选择&证明它是安全的

通过尝试确定您可以做出的贪婪选择来开始这个问题，例如扫描数组并找到其中的最大数字，将其附加到结果中并将其从原始数组中删除。

这个最佳选择将允许我们找到一个单位整数数组的最大可能数。

讲座声明首先找到几个贪婪的选择，确定哪个是最优的，然后证明它。如果可以证明，那么这是一个安全的举动，我们可以用它来解决问题。

### 化简为子问题，然后求解&迭代

贪婪策略在问题的较小部分上反复使用它的安全步骤，导致一个解决方案。

对于我们最大的数字数组示例，首先，该数组有 9 个数字。9 被发现是最大的数字并被删除。

我们把它分解成一个 8 位数的子问题。我们找到下一个最大的数字 8，并将其附加到结果中。`98`。

随着这种情况的继续，问题的规模不断缩小，而我们的最优贪婪选择帮助我们解决它！

### 优化

正如 Donald Knuth 所说，“过早优化是万恶之源。”

计算机科学的一个奇妙之处在于，当你从以前的错误或发现中学习时，你有能力找到越来越好的解决方案。如果有必要，算法可以被改变以运行得更快。

当解决需要最高性能的挑战性问题时，可以使用贪婪算法的这个可选步骤。

## 关闭思绪

对于软件工程师来说，用贪婪算法策略解决算法问题可能是非常有用和简单的技术。讲座提供了大量关于其他类型的可以解决的问题的信息，最小再填充问题，或分数背包。它很好地概述了运行时并分析了解决它们的算法，然后要求我们在编程作业中解决一些问题。

总的来说——项目作业让我回到了在 JMU 计算机实验室做作业的时候，怀旧是不真实的，我期待着重温我的教授那时教给我们的所有知识！

敬请关注第 4 周！！