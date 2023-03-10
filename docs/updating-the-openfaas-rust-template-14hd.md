# 更新 openfans rust 模板

> 原文：<https://dev.to/jonstodle/updating-the-openfaas-rust-template-14hd>

最近，我们开始考虑在工作中使用 OpenFaaS。我们仍然只是在实验阶段，但我真的很喜欢我目前所看到的。

我最近也爱上了 Rust，很想和 OpenFaaS 一起使用，幸运的是，社区提供了一个模板，可以随时使用。然而令我沮丧的是，我很快发现了一个小缺陷。包含我的函数的 Docker 容器的大小刚刚超过 1 GB。这对于 30 行代码来说太大了，即使考虑到我有三个依赖项。

我开始研究是什么使容器变得如此之大。它很快显示自己是基地形象。OpenFaaS 模板使用官方的 Rust 映像来构建和运行这个函数，但是问题是，这个映像是用来构建应用程序的，而不是用来运行它的。Rust 映像中包含了很多东西，在正常运行应用程序时并不需要。例如，不需要货物、rustc 或工具链的任何其他部分。

Rust 映像是基于 Debian Stretch 的，所以我修改了 Dockerfile 文件，使用 Rust 映像作为构建步骤。然后，它根据`debian:stretch-slim`将编译后的二进制文件复制到一个新的更小的映像中。

这使得图像大小约为 100 MB，只有大小的 1/100！

理想情况下，我想把它放在 Alpine 映像上，但是我正在构建的应用程序的一部分依赖于它所基于的操作系统。意思是:如果它建立在 Debian 上，它需要 Debian 来运行。我可能会尝试制作一个阿尔卑斯山建筑图像来解决这个问题。

在我精简了生成的图像的大小之后，我还检查了 Docker 文件的其余部分，并使其与官方 Go 模板的设置保持一致。让这两个映像尽可能相似更合适，因为它们都是“系统语言”，没有或很少依赖外部。

你可以在 [my Github](https://github.com/jonstodle/openfaas-rust-template) 上找到更新后的 OpenFaaS Rust 模板，你可以通过运行
在你的 OpenFaaS 项目中使用它

```
faas-cli template pull https://github.com/jonstodle/openfaas-rust-template
faas-cli new oxidize --language rust 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！