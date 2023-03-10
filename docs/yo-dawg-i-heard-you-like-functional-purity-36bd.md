# 唷老兄，我听说你喜欢功能性纯洁...

> 原文：<https://dev.to/drbearhands/yo-dawg-i-heard-you-like-functional-purity-36bd>

#### 所以我们在你的程序中加入了功能纯，这样你就可以在编写功能纯的程序时编写功能纯的程序。

我只是更好地看了一下 [Unison](https://www.youtube.com/watch?v=gCWtkvDQ2ZI) 。这是一种有趣的纯函数式语言，它将超级组合器(纯函数式编程的“变量”)存储为其内容的 SHA3-512 哈希。这实质上意味着功能不能改变。你可能会说任何可能的函数都已经存在，函数只是已知或未知。我假设没有哈希冲突，但是 512 位 SHA3 哈希发生冲突的几率相当低。

Unison 使编程行为在功能上或多或少是纯粹的(不需要改变状态，我们也不会产生不确定的输出)。他们声称，这在处理重命名、序列化、依赖关系中的版本冲突以及多个节点的并发性(非共享 MIMD)时非常有用。

这种语言有很大的潜力，但如果我不批评对现状的明确改进，我就不是我。特别是，我看到副作用的问题，这在某些时候是必要的。

我们先来看看依赖版本冲突。在功能纯度下，版本一点都不重要；只是给每个工具一个他们自己版本的依赖。hashes-as-identifier 并不是真正必要的，任何指定版本的方法都可以。另一方面，如果我们确实有副作用，同一依赖项的不同版本可能会以意想不到的方式相互冲突。

然后还有非共享 MIMD。提议的系统将允许简单的实时更新，但是有一个问题。这里介绍的并发是一个节点将一个表达式传递给另一个节点进行计算。这是在功能纯度下已知的可能性。它可能对数字运算有用，但是数据流语言和 DSL 似乎更适合这个问题。在常规的业务应用程序中，我怀疑这种形式的并发会产生很大的效果。当我们向不同的节点发送一个表达式进行求值时，我们希望计算的预期成本高于通信成本，否则就没有任何意义。我很少遇到这样的计算，*除非它们是全状态的*，就像数据库查找一样，但是那会使这种形式的并发变得不安全。不过，我很乐意被证明是错的。

对我来说，重命名和序列化方面的改进看起来很可靠，但与转换语言的成本相比，它们似乎不是大问题。

总而言之，Unison 看起来肯定很有趣，但是我还不相信它会成为 FP 领域的“下一个大事件”,或者它会让迫切的程序员学习 FP。