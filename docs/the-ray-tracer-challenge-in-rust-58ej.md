# Rust 中的射线追踪挑战

> 原文：<https://dev.to/magnusstrale/the-ray-tracer-challenge-in-rust-58ej>

## 时候到了..

今年夏天，我开始通过使用光线追踪挑战书来学习 Rust，并在 Rust 中实现它。这是一个兼职项目，但非常有趣和具有挑战性。两个月前，我还承诺在 GitHub 上发布我的代码，所以这是早该做的。代码是不完整的，有几个章节我还没有完成，所以期待看到更多的变化。

不管怎样——现在我要把我在《铁锈》中的磕磕绊绊暴露给世人。经过一番考虑后，我决定原封不动地发布整个 Git 回购，而不是从当前位置开始。看着我以前做的一些事情有点尴尬，但我完全预计代码的当前状态会让我未来的自己尴尬，所以管它呢...

[https://github.com/magnusstrale/raytracer](https://github.com/magnusstrale/raytracer)

## 对代码的一些思考

在回顾 Git 历史时，我发现有趣的一件事是我在决定如何处理矩阵(光线跟踪的基本部分)时的矛盾心理，更具体地说，是如何将数据存储在矩阵结构中。最大的障碍是 Rust 中的数组必须有固定的大小，因为数组的大小是其类型的一部分。你要么必须使用一个 [Vec](https://doc.rust-lang.org/std/vec/struct.Vec.html) (vector，类似于一个可变大小的数组)，要么根据数组大小提供不同的函数来调用。

我现在的解决方案(也许将来我会再次改变主意)是在内部总是使用 4x4 数组来存储数据，即使是在使用 2x2 或 3x3 矩阵时。做出这个决定的主要原因是我想为 Matrix 实现[复制特性](https://doc.rust-lang.org/std/marker/trait.Copy.html)(这在使用 Vec 时是不可能的)。这使得矩阵运算的表达式看起来更干净，因为我不需要放入大量的&来借用数据。请看[这里的](https://github.com/magnusstrale/raytracer/blob/1c2e19ec913c70ef7161608a67d09c7d5861d662/src/matrix.rs#L561)一个“干净”矩阵乘法的例子。

在代码库的早期阶段，我使用了 Vec，将来我可能会再次切换回 Vec。

## 下一步

*   完成这本书。RTC 书还有几章，我打算一直读到最后。

*   性能优化。现在，我只是尽可能保持代码的整洁，但我期待着性能的调整。我将讨论 Vec 与阵列、复制可能带来的性能影响等。

*   光线跟踪的目标 WASM /浏览器。我想获得更多的经验，与网页组装和生锈的工具链生产 WASM。

*   建立一个光线跟踪网站。有一些相当简单的东西，可以交互地创建一个世界，然后从浏览器中运行的 WASM 包中获得一个漂亮的光线跟踪图像。当然，对于任何服务器端编码，我都会使用 use Rust。

## 结束语

在这里随意浏览代码，下载并使用它。我将非常乐意听取任何关于如何改进代码的意见，特别是如果 Rust 中有任何可以用来减少代码量或使其更符合习惯的东西。