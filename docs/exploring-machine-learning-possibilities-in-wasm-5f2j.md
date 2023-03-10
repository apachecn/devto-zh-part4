# 在 WASM 探索机器学习的可能性

> 原文：<https://dev.to/netguru/exploring-machine-learning-possibilities-in-wasm-5f2j>

#### 这篇文章是我们最近对 WASM & ML 实验的一个简短总结。

目标是在 WASM 运行一个简单的 ML 模型，然后用现有的 JavaScript ML 库对其进行基准测试。我们的机器学习团队提供了三种格式的基本灰度过滤器模型:CoreML、Tensorflow 和 PyTorch。

我们首先尝试 PyTorch，因为它看起来很有希望。我们发现了很好的铁锈绑定-[https://github.com/LaurentMazare/tch-rs](https://github.com/LaurentMazare/tch-rs)-似乎非常容易使用。但是经过一些修补之后，事实证明不可能从 PyTorch 中以一种可以在 Rust 中轻松导入的形式导出整个模型。在加载 ML 团队提供的数据之前，我们基本上必须从头实现模型。这对于我们的实验来说有点过了，所以我们继续。

接下来我们看了 CoreML，但是很快发现实际上没有 Rust 或者其他我们可以编译到 WASM 的库。我不确定这是因为 CoreML 是由苹果公司制造的(因此是专有的)，还是因为社区没有那么大，也没有人对其他语言的库感兴趣。如果是第二种情况，也许值得在未来研究一下。

我们最后的机会是 Tensorflow，它开始得很好。尽管它对 WebAssembly 没有一流的支持(这是一项正在进行的工作:[https://github.com/tensorflow/tfjs/issues/1497](https://github.com/tensorflow/tfjs/issues/1497))，但它**对 Rust-[https://github.com/tensorflow/rust](https://github.com/tensorflow/rust)有官方支持。
我们能够快速构建一个简单的应用原型，在 a *中加载 Tensorflow 模型。pb 格式，将图像作为张量传递给它，最后将神经网络的输出保存到文件系统。它工作得非常好，所以我们试图将其移植到 WebAssembly。多亏了`wasm-pack`([https://github.com/rustwasm/wasm-pack](https://github.com/rustwasm/wasm-pack))，生锈真的很容易。
不幸的是，这时我们遇到了一个主要障碍。原来 Tensorflow 依赖于一个名为“aligned_alloc”的库，该库不会编译到 WebAssembly，很可能是由于一些系统相关的功能无法移植到浏览器。**

我目前的结论是，将大型库移植到 WASM 通常会很棘手。内存分配的差异会在底层代码中产生冲突，有些库根本无法编译。另一个解决方案是从头开始编写一个神经网络，只使用基本的库和语言级别的构造。这种方法在[https://ngoldbaum.github.io/posts/python-vs-rust-nn](https://ngoldbaum.github.io/posts/python-vs-rust-nn)进行了探索。它很可能编译没有问题，但需要更多的时间来开发。