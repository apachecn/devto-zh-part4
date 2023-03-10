# 我对大 O 的回忆以及理解它将如何使所有的开发变得更容易。

> 原文：<https://dev.to/jonathanmkpt/my-recall-of-big-o-and-how-understanding-it-will-make-all-development-easier-1kkl>

自从我的上一篇文章以来，我一直在断断续续地回到代码流中。

最近，我在柯尔特·斯蒂尔的 Javascript Masterclass 中重新研究了数据结构和算法。到目前为止，这些概念开始变得有意义，但要真正强化这些想法，还需要回忆，所以现在开始

在计算机科学中，大 O 是用来描述算法效率的度量。根据使用的数据结构、算法和语言，可能会出现最好、最差和一般的情况。

常见的运行时包括 O(N)，O(log N)，O(N log N)，O(N^2)和 O(2^N).

运行时由编程中使用的操作和内存量(例如 O(1) a 常数)决定。

理想情况下，在部署程序时考虑运行时会有所帮助。对于浏览器中的 javascript，根据浏览器本身的不同，可能会出现不同的运行时。

关于内存(O(1))，也称为空间复杂度，可以影响大 O，因为计算机必须保留内存用于操作。

最佳和最差情况由算法试图做的事情决定。例如，遍历一个接近排序的项目数组进行冒泡排序将是 O(N ),然而在大多数情况下，项目必须先排序，因此我们将使用 O(N^2 ),因为它必须递归遍历并检查每个未排序的元素。

无论如何，这是我第一次尝试理解大 O 理论，非常感谢建设性的批评和指导。

如果有什么我遗漏了或者可以详细说明的，请让我知道。

再次感谢未来的软件工程师乔纳森·托马斯