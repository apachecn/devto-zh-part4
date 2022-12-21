# 面试准备笔记

> 原文：<https://dev.to/iafonov/interview-preparation-notes-408d>

# 面试准备笔记

这些是我在准备 CS-101 式白板面试(又名 Google/FB/Leetcode 式面试)时使用的编辑笔记。

白板式面试有褒贬不一的态度——有人喜欢；有些人讨厌他们。我的策略非常简单——你可以抱怨这个世界是一个不公平的地方，或者你可以鞭策自己，学习如何倒置二叉树😉

对我来说是好处，对许多人来说是最大的缺点——这些面试似乎会使以前的特定技术和测试基础的经验“无效”。在 OpenBSD 上设置 k8s 的经验并不重要，但是理解各种技术如何在幕后工作非常重要。通用流程提供了完全转换技术堆栈和尝试新事物的能力，而不受特定体验的束缚。

在进行编码面试准备时，我注意到了一件意想不到的事情——我觉得它让我成为了一名更好的程序员。从第一遍开始，我开始思考得更快，代码更干净。系统设计促使我学习一堆我在实践中使用的东西，但是从来没有费心去学习它们是如何在引擎盖下工作的。行为部分包括相当多的自我反省和对过去经历的思考，以及我想从未来的职业生涯(和生活)中得到什么。

我把笔记分成几部分:

1.  [稍微讲一下心理学](#psychology)
2.  [行为](#behavioral)
3.  [编码](#coding)
4.  [系统设计](#systemdesign)
5.  [给面试官的问题](#questions)
6.  [报价谈判](#offer)

## 心理学

我觉得在一个特定的公司和职位上找工作是有缺陷的。面试是抽签(对双方都公平)。内化你的目标很有帮助。我的目标是自学算法，提高我的编码技能，学习数据库和大规模架构的内部知识。

在 FAANG 没有得到那份工作的目标。总是为学习而优化，结果是副产品。

## 行为

行为或领导面试是整个过程中最重要的部分。编码和系统设计主要是通过/失败阶段，而行为面试在达到平衡或提供的时候最重要。

我的收获:

*   做你自己
*   善良一点
*   不要撒谎和编造故事(即使是无意识的)
*   花时间自我反省和思考过去
*   想想未来，想从职业生涯中得到什么

我没有发现 STAR 和其他“智能”框架有什么帮助。如果你没有太多的经验，也许它们会起作用——如果你有故事要讲——你就可以走了。

## 编码

我准备了一次经典的白板式面试。我训练自己在纸上或白板上写代码，而不会运行或调试代码。从第一遍开始，我就花时间训练自己写出干净易读的代码。

沟通是至关重要的一部分。在编写代码之前，你应该能够解释你要做什么，令人惊讶的是，有时这是最难的部分。当我在做“普通”编程时，我会做大量的实验，有时开始写代码时，我会想着在进行中或以后重构或重写它。如果你有 45 分钟，白板和一双眼睛盯着你，那就不行。

你必须首先促使自己思考，解释你的想法，然后才开始写代码。

帮助我完成所有这些部分的是形式化算法来解决算法问题。我认为这是一件有点私人的事情，我建议你在安顿下来之前尝试各种方法。但是在你安定下来之后——总是推动自己去跟随它。

### 我解决一个算法问题的算法

所有这些步骤都是必不可少的，即使我觉得我可以跳过其中一个——我也总是鞭策自己，避免那样做。

有一些情况下，我跳过了一步，但在解决问题的几个步骤后才意识到我错了，需要返回并从跳过的步骤开始。

*   用你自己的话重复问题，并确认你理解正确
*   构建 2-3 个常规流程示例——编写它们
*   列出关键案例，问面试官期望什么(你可以自己随意编造，但要向面试官提及)
*   概述方法并大声思考它的时间和空间复杂性
    *   如果你想不出办法，试着用手解决这个问题
*   向面试官(或你自己)确认这种方法是一个好的起点。如果没有，重复前面的步骤。
*   非常缓慢地编写代码
*   使用您的示例手动调试。如果你能执行代码——用你的例子作为单元测试
*   优化(在 80%的情况下，使用散列/集合来简化算法，用时间换取空间😉)
*   重复

如果一切顺利——你很好，如果你想不出解决办法——推自己一把。仔细倾听直接和间接的暗示。不要放弃。

### 模拟采访

模拟面试有助于焦虑管理、时间安排和沟通。我用[interview . io](//interviewing.io)在那里得到了几次面试的机会。你可以利用朋友或雇人来做这件事。

你也可以试着去你不太感兴趣的公司面试。我并不为此感到难过。公司面试你，作为回报，它得到了一个雇用你的机会。

### 本书

我读过《破解编码面试》，但不喜欢。很糟糕。它太简单了，太高级了，它的写作很奇怪，给人一种这件事很容易的错觉。不是的。

我建议找一本好的 CS 101 或经典算法教科书，读一读，花些时间写些基本的东西。

对我有用的书是杰夫·埃里克森的《算法》。很优秀——很简洁，用伪代码来解释编码部分，很容易读懂(是给一天结束的学生看的)。

Skienna 的《算法设计手册》也很棒，但是有点难读，你可能需要自己挑选某些相关的部分。如果你有足够的时间，它可以工作得很好。

### 基础知识-我的最爱

这些是我最喜欢的基本问题。它们是解决更难问题的基础，所以有必要确保你能有意识地，在完全理解的情况下，像在黑板上写字或在笔记本电脑上打字一样快地编写它们。

我通常的做法是在做更复杂的事情之前先解决 2-3 个问题作为热身。

很多这样的问题在筛选/实际面试中都会用到。

*   数组:[二分搜索法](https://leetcode.com/problems/binary-search/)，[搜索插入位置](https://leetcode.com/problems/search-insert-position/)，【T12 求和，[3 求和](https://leetcode.com/problems/3sum/)，[非递减数组](https://leetcode.com/problems/non-decreasing-array/)，[第一个坏版本](https://leetcode.com/problems/first-bad-version/)，[最大子数组](https://leetcode.com/problems/maximum-subarray/)，[子数组和等于 K](https://leetcode.com/problems/subarray-sum-equals-k/submissions/) ，[旋转数组](https://leetcode.com/problems/rotate-array/)，[在旋转数组中搜索](https://leetcode.com/problems/search-in-rotated-sorted-array/)，[从排序后的数组中删除重复的](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
*   矩阵:[旋转图像](https://leetcode.com/problems/rotate-image/)
*   字符串:[回文](https://leetcode.com/problems/valid-palindrome/)、[变位词](https://leetcode.com/problems/valid-anagram/)、[K-回文](https://leetcode.com/problems/valid-palindrome-ii/)、[滑动窗口](https://leetcode.com/problems/longest-substring-without-repeating-characters/)、[平衡括号](https://leetcode.com/problems/valid-parentheses/)
*   图表*: DFS，BFS，[迪杰斯特拉](https://leetcode.com/problems/cheapest-flights-within-k-stops/)
*   树:DFS，BFS， [LCA](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) ，[层次顺序遍历](https://leetcode.com/problems/binary-tree-level-order-traversal/)，[二叉树最大路径和](https://leetcode.com/problems/binary-tree-maximum-path-sum/submissions/)，[从根到叶的和](https://leetcode.com/problems/sum-root-to-leaf-numbers/)，[将树转换为链表](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list/)，[序列化/反序列化树](https://leetcode.com/problems/serialize-and-deserialize-bst/)
*   链表:[反转](https://leetcode.com/problems/reverse-linked-list/)，找到中间元素，移除元素
*   堆:自己构建一个并理解它是如何工作的，[数组中第 k 个最大的元素](https://leetcode.com/problems/kth-largest-element-in-an-array/)
*   组合学:[排列](https://leetcode.com/problems/permutations/)，[子集](https://leetcode.com/problems/subsets/)
*   动态编程**: [硬币兑换](https://leetcode.com/problems/coin-change/)，[生成括号](https://leetcode.com/problems/generate-parentheses)，[递增子序列](https://leetcode.com/problems/longest-increasing-subsequence/)，[买卖股票](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
*   杂项:[二进制加法](https://leetcode.com/problems/add-binary/)，[会议室](https://leetcode.com/problems/meeting-rooms-ii/)

*图形问题最难的部分是看到它是一个图形问题。如果你发现这是一个图表问题，你就完成了 90%

**动态规划问题是最糟糕的面试问题。他们毫无用处。你要么知道诀窍，要么不知道。在面试压力下，几乎没有人能想出一个最佳的解决方案。但是，基本的了解帮助很大。

### 杂项

*   买一个真正的白板和超细记号笔，要有 4k 的分辨率。
*   把它当成一项运动，经常练习。需要的时候休息。你可以称之为竞争性面试。

### 链接

*   史蒂夫·耶格——得到谷歌的那份工作
*   杰夫·埃里克森-算法
*   [图沙尔罗伊-编码变得简单](https://www.youtube.com/channel/UCZLJf_R2sWyUtXSKiKlyvAw)

## 系统设计

最好的建议是想办法获得在现实生活中设计系统的经验。

我在这里作弊了——在我职业生涯的很大一部分时间里，我都是开发人员，对如何在基础架构上构建 web 应用程序有相当好的理解。然而，我需要时间来系统化知识和建立我的方法。同样，沟通是最重要的部分。如果你不能解释你的方法——不管它是对是错。

我建议，如果您在实践中没有使用特定的技术，就避免提及它们，而是使用通用的工具。因此，如果您需要分布式队列，并且您从未使用过单名组件“分布式队列”,就不要深究细节了。

### 我的方法

*   理解系统的目标。用你自己的话重复一遍
*   问好问题。定义 **MVP** 。考虑特性和需求
*   确定系统的范围
*   找出面试官对哪些部分感兴趣
*   自己编造数字和驱动需求
*   从非常高的水平开始
*   深入挖掘和提炼
*   重复
*   继续前进，直到时间结束

### 我的最爱

这些都是我最喜欢的问题。我在实践它们和做研究的时候学到了很多。

*   原子计数器
*   限速器
*   分布式队列
*   视频上传/服务基础设施
*   全文搜索
*   实时消息传递(有或没有持久性)
*   分布式日志收集器
*   你最喜欢的社交网络
*   你最喜欢的搜索引擎

在纸上或白板上练习。

### 阅读和链接

圣杯- [设计数据密集型应用](https://www.amazon.com/Designing-Data-Intensive-Applications-Reliable-Maintainable/dp/1449373321)，这本书是杰出的。即使你没有准备面试，或者你不打算和数据打交道，我也建议你读一读。这是近代最好的技术书籍之一。

令人惊讶的是，我发现阅读 Redis 文档非常有用。它很容易阅读，有很多例子，读起来更像是一本关于一般存储系统的书，而不是关于 DB 的文档。

更多链接:

*   [再版文献](https://redis.io/documentation)
*   [BigTable 白皮书](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf)
*   [DynamoDB 白皮书](http://courses.cse.tamu.edu/caverlee/csce438/readings/dynamo-paper.pdf)

## 面试官的问题

我认为面试时这些问题对双方都没用。提出问题的最佳时间是在报价和报价被接受之间。如果你得到了一份工作，你总是可以要求和面试官谈 30-45 分钟，进行一次“逆向面试”——开一次会议，专门问一些关于公司的问题。为这次会面做好准备，仔细聆听，注意小细节——最后你就站在了销售台上。

至于通常的 5 分钟时间(最好的情况),准备一套通用但巧妙的问题，让他们来填满这段时间。

## 要约谈判

有一个好的替代方案(又名 BATNA -谈判协议的最佳替代方案)。最好的和有点反直觉的选择是在你目前的工作中做得很好，很开心。

## 结束

你的经历可能不一样😉