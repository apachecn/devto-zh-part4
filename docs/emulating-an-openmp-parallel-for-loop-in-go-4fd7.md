# 在 Go 中模拟 OpenMP 并行 For 循环

> 原文：<https://dev.to/downey/emulating-an-openmp-parallel-for-loop-in-go-4fd7>

*封面照片由[丹尼斯·内沃斋](https://unsplash.com/@dnevozhai)在[Unsplash](https://unsplash.com)T5 拍摄*

在佐治亚理工学院[高性能计算](https://www.omscs.gatech.edu/cse-6220-intro-hpc)课程的几个实验室中，我们使用 [OpenMP](https://www.openmp.org/) 和 C 实现了一些并行算法，如 [Helman 和 Jájá list 排名算法](https://downey.io/blog/helman-jaja-list-ranking-explained/)。虽然 C 语言很有趣，但我最近一直在尝试更多地练习围棋，所以这让我想知道:“在围棋中有 OpenMP 的等价物吗？”

虽然我没有找到 OpenMP 的确切替代者，但是在 [Stackoverflow](https://stackoverflow.com/questions/36949211/is-there-a-simple-parallel-for-in-golang-like-openmp) 上的帖子让我意识到我可以使用 [Goroutines](https://golang.org/doc/effective_go.html#goroutines) 来完成类似的目标。具体来说，我感兴趣的是创建一个简单的并行 for 循环，尝试重新实现 Go 中的一些实验。

## C 和 Go 中的并行 For-Loop 示例

下面是一个简单的 C 并行 for 循环，使用 OpenMP 的 [omp parallel for](https://www.ibm.com/support/knowledgecenter/SSGH2K_13.1.2/com.ibm.xlc1312.aix.doc/compiler_ref/prag_omp_parallel.html) 指令。它正在执行 Helman-JáJá list-ranking 算法的一部分。这里需要注意的关键是，在下面的例子中，`rank` / `list`数组中的每个元素都是独立操作的。这允许每个循环迭代安全地并行执行。

```
#pragma omp parallel for for (int i = 0; i < n; i++) {
    long currIdx = next[i];

    if (currIdx != NIL) {
        rank[currIdx] += headPrefixData[sublists[currIdx]];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看这在围棋中可能是什么样子。忽略数据结构可能有点不同的事实——算法背后的意图是相同的。

```
var wg sync.WaitGroup
wg.Add(n)
for i := 0; i < n; i++ {
    go func(i int) {
        defer wg.Done()
        (*list)[i].prefixData += sublists[(*list)[i].sublistHead].prefixData
    }(i)
}
wg.Wait() 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做以下事情:

1.  创建一个[等待组](https://golang.org/pkg/sync/#WaitGroup) ( `wg`)，并告诉它等待`n` Goroutines 完成
2.  为我们想要并行化的循环的每次迭代生成一个 Goroutine
3.  告诉每个 Goroutine，一旦它执行了前缀添加操作，就让 WaitGroup 知道它已经完成了
4.  告诉等待组阻塞，直到收到来自`n` Goroutines 的反馈

#### 关于 Goroutine 调度的一个注记

您可能想知道为什么可以在上面的代码中创建潜在的大量(`n`)go routine。简而言之，Goroutines 在硬件线程间被复用，并由 Go 的调度程序独立于操作系统进行调度。[这篇博文](https://rcoh.me/posts/why-you-can-have-a-million-go-routines-but-only-1000-java-threads/)很好地解释了围棋如何处理数百万道围棋程序。这与 [OpenMP 通过在线程间分配大量工作来处理调度](https://www.dartmouth.edu/~rc/classes/intro_openmp/schedule_loops.html)的方式略有不同。然而，归根结底，它们都是将大量工作分配给更小、数量固定的可用处理器的手段。

如果你真的想深入了解，Kavya Joshi 在 GopherCon 2018 上的这个演讲提供了一个很好的视角来了解 Goroutine 调度程序下正在发生的事情。

## 总结

好了，你知道了。它比 OpenMP 版本更罗嗦，但我认为它真的很酷，你可以使用几个 Go 的标准库完成几乎相同的行为。也就是说，OpenMP 范例非常像 C 语言，而且我认为不是惯用的 Go。🙃

如果任何有围棋经验的人正在阅读这篇文章，请在下面的评论中分享你是如何编写并行 for 循环的(或者用另一种方式完成同样的目标)!