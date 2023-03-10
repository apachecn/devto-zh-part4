# 针对 Java、C++和 Python 程序员的 20 多个链表面试问题

> 原文：<https://dev.to/javinpaul/review-these-linked-list-interview-questions-before-your-java-python-or-c-programming-interviews-35o4>

*披露:这篇文章包括附属链接；如果您从本文提供的不同链接购买产品或服务，我可能会收到报酬。*

大家好，我一直在为那些积极寻找 Java、Python 或 C++开发人员工作的程序员分享许多编码面试问题，特别是初学者、初级开发人员和刚刚毕业但没有实际工作经验的计算机工程师。

在过去，我已经分享了一些[数据结构问题](https://dev.to/javinpaul/50-data-structure-and-algorithms-problems-from-coding-interviews-4lh2)、[字符串算法问题](https://dev.to/javinpaul/top-20-string-coding-problems-from-programming-job-interviews-493m)和一些有用的[在线课程](https://hackernoon.com/10-data-structure-algorithms-and-programming-courses-to-crack-any-coding-interview-e1c50b30b927)为编程工作面试做准备，今天我将分享一份来自编码面试的**常见链表问题列表**。

数据结构是任何编程工作面试中最重要的部分之一，通常也是选择或拒绝候选人的原因，这就是为什么复习和练习这些基于数据结构的问题会让你比你的竞争对手更有优势。

它还会让你成为一名更好的程序员，因为你在解决这些问题的同时发展了逻辑和编码意识，这对你的编程生涯大有帮助。

解决这些编码问题不仅能帮助你更好地学习编程语言和工具，还能磨练你解决问题的技巧，这对于任何专业开发人员都是非常重要的。

毕竟，你需要提高解决问题的技巧，以至于你可以解决任何未知的问题，并将其转化为代码，练习这些问题有助于你实现这个目标。

## 什么是链表？

[链表](http://www.java67.com/2017/06/5-difference-between-array-and-linked.html)是补充数组数据结构的另一种常见数据结构。与数组类似，它也是一种线性数据结构，以线性方式存储元素。

然而，与[数组](http://www.java67.com/2014/08/what-is-array-data-structure-in-java.html)不同，它不将它们存储在连续的位置；相反，它们分散在内存中的每个地方，内存使用节点相互连接。

> **链表只不过是一个节点列表**，其中每个节点包含存储的值和下一个节点的地址。

由于这种结构，**很容易在链表**中添加和删除元素，因为你只需要改变链接，而不是创建[数组](https://javarevisited.blogspot.com/2015/06/top-20-array-interview-questions-and-answers.html)，但是搜索很困难，通常需要 O(n)时间才能在单链表中找到一个元素。

以下是链表数据结构的一些重要属性:

1.  链表是一种递归数据结构，这意味着你可以使用递归来解决基于链表的问题。

2.  在链表中，每个节点都有下一个节点的地址，这意味着你不需要像数组那样的大块连续内存来创建一个长链表。你仍然可以用零散的记忆创建它们。

3.  在链表中添加或移除元素更容易，因为你只需要移除或重新指向链接，而不像数组那样需要移动元素。将一个元素添加到 head 需要 O(1)时间。

4.  在链表中搜索是困难的，搜索一个链表需要 O(n)时间，因为你需要遍历链表到达目标节点。

这篇[文章](http://javarevisited.blogspot.sg/2013/07/difference-between-array-and-linked-list-java.html)提供了更多关于数组和链表数据结构之间区别的信息。

它也有多种形式，比如单链表，允许你单向遍历(正向或反向)；一个**双向链表**，允许你双向遍历(向前和向后)；最后是**循环链表**，形成一个循环。

## 如何解决链表编码问题？

为了解决基于链表的问题，很好地了解[递归](https://javarevisited.blogspot.com/2017/03/how-to-reverse-linked-list-in-java-using-iteration-and-recursion.html)是很重要的，因为**链表是一种递归数据结构**。

如果从链表中取出一个节点，剩下的数据结构仍然是链表，正因为如此，许多链表问题有比迭代更简单的[递归解](https://javarevisited.blogspot.com/2017/04/recursive-binary-search-algorithm-in-java-example.html)。

它们也可以通过分而治之的方法来解决，这种方法将问题分解成子问题，直到你能够解决它们。

> 例如，为了反转一个链表，你打破链表，直到你只有一个节点，在这一点上，你知道如何反转一个节点的链表，它只是同一个节点。

这非常类似于递归，实际上，你能解决的最小的子问题成为递归解决方案的基础。

顺便说一句，如果你没有数据结构的基本知识或者最近没有更新过，解决这些基于链表的编码问题是没有意义的。那样的话，我建议你先通过好的[数据结构和算法课程](https://javarevisited.blogspot.com/2018/11/top-5-data-structures-and-algorithm-online-courses.html)或者[书](https://javarevisited.blogspot.com/2015/07/5-data-structure-and-algorithm-books-best-must-read.html)来修改概念。

如果您需要建议，以下是我的一些久经考验的资源，用于深入学习数据结构和算法:

1.  [**数据结构和算法:Java 开发人员使用 Java**](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fdata-structures-and-algorithms-deep-dive-using-java%2F) 的深度探讨
2.  [**Python 中的算法和数据结构**](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Falgorithms-and-data-structures-in-python%2F) 给热爱 Python 的人
3.  [**JavaScript 算法与数据结构大师班**](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=508237.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fjs-algorithms-and-data-structures-masterclass%2F) 由柯尔特·斯蒂尔为 JavaScript 程序员开设
4.  [**掌握数据结构&使用 C 和 C++** 算法](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fdatastructurescncpp%2F)对于擅长 C/C++的人
5.  [**Java 中的数据结构:一个访谈复习者**](https://www.educative.io/collection/5642554087309312/5724822843686912?affiliate_id=5073518643380224) 刷新 Java 中重要的数据结构和算法概念。

而且，如果你更喜欢书，没有比托马斯·h·科尔曼的**算法导论更好的了。这是关于数据结构和算法的最全面的书籍之一，对程序员和软件开发人员很有用。**

 **[![](img/4538769c94e192b2283888fe2ecdafeb.png)](https://www.amazon.com/Introduction-Algorithms-Edition-Thomas-Cormen/dp/0262033844?tag=javamysqlanta-20)

## 20+编码访谈常见链表问题

在不浪费你更多时间的情况下，这里有一些编码面试中最常见和最流行的链表面试问题。我已经尽可能地链接了解决方案，但我建议你首先尝试自己解决问题，这将使你受益，因为你会思考和学习。一旦你解决了问题或者在尝试后卡住了，你可以看看解决方案并从中学习。

1.  **如何一次找到单链表的中间元素？(** [**解**](http://javarevisited.blogspot.sg/2012/12/how-to-find-middle-element-of-linked-list-one-pass.html) **)**

2.  如何在没有递归的情况下反转一个单链表？( [**解**](http://javarevisited.blogspot.sg/2017/03/how-to-reverse-linked-list-in-java-using-iteration-and-recursion.html) **)**

3.  **在一个未排序的链表中，如何去除重复的节点？(** [**解**](https://www.geeksforgeeks.org/remove-duplicates-from-an-unsorted-linked-list/) **)**

4.  如何求单链表的长度？( [**解**](http://javarevisited.blogspot.sg/2016/05/how-do-you-find-length-of-singly-linked.html) **)**

5.  如何检查一个给定的链表是否包含循环？如何找到循环的开始节点？( [**解**](http://javarevisited.blogspot.sg/2013/05/find-if-linked-list-contains-loops-cycle-cyclic-circular-check.html) **)**

6.  如何反转一个链表？( [**解**](http://www.java67.com/2016/07/how-to-reverse-singly-linked-list-in-java-example.html) **)**

7.  **如何在单链表中找到倒数第三个节点？(** [**解**](http://javarevisited.blogspot.sg/2016/07/how-to-find-3rd-element-from-end-in-linked-list-java.html) **)**

8.  **如何用 Stack 求两个链表的和？(** [**解**](https://www.geeksforgeeks.org/sum-of-two-linked-lists/) **)**

9.  如何就地反转一个链表？(解)
    ([http://www . Java 67 . com/2017/06/5-difference-between-array-and-linked . html)* *)* *](http://www.java67.com/2017/06/5-difference-between-array-and-linked.html)**)**)

10.  **如何移除链表末尾的第 n 个节点？(** [**解**](https://leetcode.com/problems/remove-nth-node-from-end-of-list/solution/) **)**

11.  **如何合并两个已排序的链表？**(解决方案)

12.  **如何在链表中间添加一个元素？**(解决方案)

13.  在 Java 中如何对链表进行排序？( [**解**](http://www.java67.com/2016/02/how-to-sort-linkedlist-in-java-example.html) **)**

14.  **数组和链表的区别？(** [ **回答**)

15.  如何将排序列表转换成二叉查找树？( [**解**](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/solution/) **)**

16.  **给定一个链表和值 *x* ，对其进行划分，使得所有小于 *x* 的节点出现在大于或等于 *x* 的节点之前。(** [**解**](https://leetcode.com/problems/partition-list/solution/) **)**

17.  **如何从一个整数链表中删除所有与给定值匹配的元素？**(解决方案)

18.  **如何找到两个单链表开始交集的节点。(** [**解**](https://leetcode.com/problems/intersection-of-two-linked-lists/solution/) **)**

19.  **如何检查给定的链表是否是回文？**(解决方案)

20.  **如何去除排序链表中的重复项？(** [**解**](https://leetcode.com/problems/remove-duplicates-from-sorted-list/solution/) **)**

这些问题将帮助你发展解决问题的技能，并提高你对链表数据结构的了解。

如果你在解决这些链表编码问题上有困难，那么我建议你通过参加 [**数据结构和算法:使用 Java**](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fdata-structures-and-algorithms-deep-dive-using-java%2F) 课程来更新你的数据结构和算法技能。

[![](img/4b6bbe3d03bdbd5fc6063b11f5db4190.png)](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fdata-structures-and-algorithms-deep-dive-using-java%2F)

如果这些问题还不够的话，你还可以看看这张 [**30 个链表式面试问题**](http://javarevisited.blogspot.sg/2017/07/top-10-linked-list-coding-questions-and.html) 的清单，以获得更多的练习题。

### 对编码面试有用的资源

如果你需要一些有用的资源来做好你的编程和编码工作面试，这里有一些你应该看看的在线课程和书籍:

1.  [数据结构和算法:使用 Java 进行深入研究](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fdata-structures-and-algorithms-deep-dive-using-java%2F)
2.  [寻找编码面试:编码问题的模式](https://www.educative.io/collection/5668639101419520/5671464854355968?affiliate_id=5073518643380224)
3.  [破解编码面试本](http://www.amazon.com/Cracking-Coding-Interview-6th-Edition/dp/0984782850/?tag=javamysqlanta-20)
4.  [掌握编码面试:数据结构+算法](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fcourse%2Fmaster-the-coding-interview-data-structures-algorithms%2F)
5.  约翰·桑梅兹准备工作面试

我在这篇文章里分享了很多资源，供 [Java](https://javarevisited.blogspot.com/2018/07/top-5-websites-to-learn-coding-in-java.html) 、 [Python](https://javarevisited.blogspot.com/2019/09/5-websites-to-learn-python-for-free.html) 、 [C++开发者](https://javarevisited.blogspot.com/2020/07/top-10-courses-to-learn-c-in-depth-best.html)学习数据结构和算法。

这些资源有些是免费的，有些是付费的，对于付费的，如果你使用本文中的链接购买它们，我会得到报酬，但只有在你真的需要它们，并且只有在观看预览和阅读样本章节后，才购买它们。

它们是最好的资源，但对你来说，重要的是要与作者或指导者联系，以充分利用它们，因此购买你与他们联系的资源。

### 现在你离编程面试又近了一步

这些是数据结构和算法之外的一些最常见的问题，有助于你在面试中表现出色。

我在我的[博客](http://java67.com/)上也分享了很多数据结构和算法的问题，所以如果你真的感兴趣，你可以随时去那里搜索。

这些常见的[编码](http://www.java67.com/2018/05/top-75-programming-interview-questions-answers.html)、[数据结构](https://hackernoon.com/50-data-structure-and-algorithms-interview-questions-for-programmers-b4b1ac61f5b0)和[算法](https://dev.to/javinpaul/50-data-structure-and-algorithms-problems-from-coding-interviews-4lh2)问题* *s* *是你需要知道的，以成功面试任何公司，无论大小，任何级别的编程工作。

如果你正在寻找一份 [Java](https://medium.com/javarevisited/10-best-places-to-learn-java-online-for-free-ce5e713ab5b2) 、 [Python](https://medium.com/javarevisited/8-advanced-python-programming-courses-for-intermediate-programmer-cc3bd47a4d19) 或 [C++编程](https://javarevisited.blogspot.com/2020/07/top-10-courses-to-learn-c-in-depth-best.html)和软件开发的工作，你可以从这份编码问题清单开始准备。

这个列表提供了准备的好话题，也有助于评估你的准备情况，找出你的强项和弱项。

良好的数据结构和算法知识对于成功编写面试代码非常重要，这也是你应该集中大部分注意力的地方。

**其他你可能喜欢的数据结构文章**
[每个程序员都应该读的 10 本算法书](http://www.java67.com/2015/09/top-10-algorithm-books-every-programmer-read-learn.html)
[面向 Java 开发人员的前 5 本数据结构和算法书](http://javarevisited.blogspot.sg/2016/05/5-free-data-structure-and-algorithm-books-in-java.html)
[20+访谈中的字符串编码问题](https://hackernoon.com/20-string-coding-interview-questions-for-programmers-6b6735b6d31c)
[100+访谈中的数据结构和算法问题](https://www.java67.com/2018/06/data-structure-and-algorithm-interview-questions-programmers.html)
[30+编码访谈中的基于数组的问题](https://javarevisited.blogspot.com/2015/06/top-20-array-interview-questions-and-answers.html)
[从 0 到 1:Java 中的数据结构&算法 -求职面试](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Ffrom-0-to-1-data-structures%2F)
[10 本书准备技术编程/编码求职面试](http://www.java67.com/2017/06/10-books-to-prepare-technical-coding-job-interviews.html)
[寻找编码面试的动态编程模式](https://www.educative.io/collection/5668639101419520/5633779737559040?affiliate_id=5073518643380224)
[寻找系统设计面试](https://www.educative.io/collection/5668639101419520/5649050225344512?affiliate_id=5073518643380224)

### 期末备注

谢谢，你坚持到了文章的结尾...祝你编程面试好运！这当然不容易，但是练习完这些问题后，你就离成功和你一直想要的工作更近了一步。

如果你喜欢这篇文章，那么请分享给你的朋友和同事，别忘了在 Twitter 上关注 [javinpaul](https://twitter.com/javinpaul) ！

P.S. -如果你需要一些免费资源，可以查看一下这个[免费数据结构与算法课程](http://javarevisited.blogspot.com/2018/01/top-5-free-data-structure-and-algorithm-courses-java--c-programmers.html)列表，开始准备。**