# 大 O 符号实用指南

> 原文：<https://dev.to/yashwanthambati/big-o-notation-complete-guide-226h>

编码时要记住的两件重要事情是

*   可读性和
*   可扩展性。

解决一个问题有很多方法，但是一个好的开发人员总是致力于更高效的代码和性能。

[![](img/56dd07c1a2221c72e4375c5ff033d4e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TCds83Ab--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3uohubbcm8hivqm0il85.PNG)

这是衡量算法/问题的时间和空间复杂性的 Big-O 部分。这有助于确定算法的复杂度，也有助于衡量算法的性能。

不同的 Big-O 术语有

*   O(1) -常数时间
*   O(n) -线性时间
*   O(n^2) -二次时间

# O(1) -常数时间复杂度

恒定的时间复杂度解释了**无论输入或输出的大小如何，执行时间和使用的资源总是相同的**。无论算法执行多少次或在哪里执行，它总是产生相同的性能。例如:

[![](img/c7f91d7c95f3f5da75f79346ba19f46f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HM2Ms7Lu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50qt88du7kcogvs4x3i5.png)

# O(n):线性时间复杂度

如果算法具有线性复杂度，那么**执行时间和/或使用的资源与输入大小**成正比。例如:

[![](img/327517c0a2af7560fcd97d32933008ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r23eH1hf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sjy6khtlfp5c5n74bsyd.png)

# O(n2):二次时间复杂度

当算法的**影响与输入大小**的平方成正比时，存在二次复杂度。

[![](img/828841d2b3cbb3c1614b4692ffb44b5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---qfwOBw3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zt3ecy5fouxu2gn0y7zq.png)

这种复杂性在冒泡排序、插入排序和选择排序等排序算法中很常见。

这里有一些棘手的例子
这是一个很好的例子，如果函数接受两个不同的输入，那么 Big-O 就变成了 **O(input1 + Input2)** 。

[![](img/97cf2d475457493eb455cdd6123df286.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9V5PjNK0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/torwgi05dqb8ypunqh0r.png)

对于上面的例子，如果它是嵌套 For 循环的**，那么 Big-O 将变为 **O(输入 1 *输入 2)** 。**

# Big-O cheatSheet 和 Graph

[![](img/ab9c520f4a3aabbf25c3aad9f6fdcdd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TkZwpgeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rima9qhnlltvnf1eaft4.png)

[![](img/c96312abd860439091fbec36f5e1f4d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ARgCxvqY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12p50w5c0x37g75uo9hx.PNG)

随意讨论更棘手的例子。
感谢您的阅读。