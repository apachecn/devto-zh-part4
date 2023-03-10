# OpenCL -第一种方法-第 1 部分

> 原文：<https://dev.to/crr0004/opencl-the-first-approach-part-1-39h5>

# 简介

最近我在机器学习方面做了很多工作和研究，包括 DeepRacer，其中一部分是找到一种方法来加速所需的计算。许多框架和库将使用 CUDA，然而像 PlaidML 这样的一些使用 OpenCL。我是开源软件的大力倡导者，最好是免费的，但是唉，所以当框架和库只使用 CUDA 加速时，这真的让我很恼火。对于那些不了解内情的人来说，NVIDIA(CUDA 的所有者)在开源社区和支持开源社区的实现方面有着可怕的历史(查看 Linux 的 NVIDIA 驱动程序)。所以我真的很想加入 OpenCL，成为我希望看到的改变的一部分，这就是支持一个对所有人免费开放的标准。

当我开始写这篇文章时，我想研究 OpenCL 以及如何使用它来实现递归，但是当我深入研究时，我意识到内容太多了。所以在这第一部分中。在这篇文章中，我将通过一个例子来介绍 OpenCL 的基本方法，以及如何在 OpenCL 调用中进行分解。继续内容！

不过很快地，所有的示例代码都可以在我的库中找到。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[crr 0004](https://github.com/crr0004)/[opencl 历险记](https://github.com/crr0004/adventures-in-opencl)

### 我写的关于 OpenCL 的附带报告

<article class="markdown-body entry-content container-lg" itemprop="text">

# opencl 中的冒险

我写的关于 OpenCL 的附带回购-[https://dev.to/crr0004/series/1481](https://dev.to/crr0004/series/1481)

# 建筑物

您将需要一个实现 OpenCL 2.0 和 cmake 的驱动程序。

这是用 clang 编译的。它应该会自动选择您的主要 OpenCL 供应商并使用它。

## 头球

OpenCL 头文件包含在这个共享存储库中，如果 CMake 找不到它们，您可以用“-DOPENCL_INCLUDE_DIRS=”覆盖该目录../../shared/"从其中一个构建目录进行构建时。如果对你来说不一样，就改变路径。

运行时，它会打印出什么设备正在使用，但如果你的供应商的实现是坏的，它可以崩溃你的驱动程序。

# 运转

从源目录运行每个示例，因为它需要获取内核文件

# 用过的库

*   https://github.com/catchorg/Catch2
*   谷歌基准[https://github.com/google/benchmark/](https://github.com/google/benchmark/)

有关各个许可证的添加，请参见许可证文件

</article>

[View on GitHub](https://github.com/crr0004/adventures-in-opencl)

# 什么是 OpenCL

OpenCL(开放计算语言)是一个开放的免版税标准，适用于跨 CPU、GPU 和其他处理器的通用并行编程，使软件开发人员能够方便高效地访问这些异构处理平台的强大功能(Khronos OpenCL 20 API 规范 2019 第 11 页)。

这些年来，出现了多种标准/库/框架/语言来迎合并行计算，它们有各种各样的优点和缺点。其中很少是硬件级的，更少是设备不可知的，也就是说，它们不是专门为设备编程的。这是 OpenCL 真正闪光的地方。它是根据一个规范实现的，所以你不会被供应商锁定(这里有一个关于扩展和优化的警告)。它是免版税的，所以供应商不需要为实现它付费，社区可以在没有什么初始摩擦的情况下出现。如果你想比较一下(双关语)，那么你可以看看 OpenGL、Vulkan、OpenGL ES(手机经常使用)、WebGL 和基本上任何 Khronos 组规范的采用情况。

展望未来，我将主要谈论 OpenCL 2.0，但是我将尝试注意 OpenCL 1.2 中不存在的部分，因为该规范直接提到了向后兼容(Khronos OpenCL 20 API Specification 2019 p . 57)，我将尝试继续下去。这适用于我在 OpenCL 上写的所有文章。

# 好的，但是我们怎样才能走得快呢？

如果你是并行计算的新手(这是我第一次深入研究)，我发现的思考它的最简单的方法是问“给定无限数量的计算单元和无限的内存，我们如何使用它们？”，所以希望这能让你意识到，我们需要做的是编写能够利用这些资源的软件。因此，要想运行得更快，我们必须想办法让所有这些计算单元以某种有用的方式对我们的内存进行操作。OpenCL 为我们提供了很多方法来实现这一点，其中一种方法是简单地将内存的一部分直接映射到一个计算单元。OpenCL 帮了我们一个大忙，它指定了当我们的内存超过计算单元时，这是如何工作的，它用“工作项”指定了“工作组”。

工作组由工作项组成，设备驱动程序指定了工作组的规模。因此，如果我们有一个包含 1024 个整数的数字列表和一个包含 64 个计算单元的设备，那么就有 16 个工作组(1024/64)。设备不必同时完成所有这些工作组，它只需最终完成即可。下图显示了它的一个基本结构，而不用担心将工作项分成组。

[![diagram of the basic structure for an opencl program](img/87d1b4b13fe140a445585cb4adafdc37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rv9UdIg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/rgunA72.png)

要进行实际的计算，你要写一个“内核”，编译它，然后把它交给你想要计算的数据所在的设备。您可以在我的存储库中看到 [naive_approach](https://github.com/crr0004/adventures-in-opencl/tree/master/naive_approach) 示例，看看这实际上是如何完成的。

如果您有 AMD 或 Intel 设备支持它，您甚至可以使用他们 SDK 中的调试器。我知道 AMD 的是 [CodeXL](https://github.com/GPUOpen-Tools/CodeXL) ，而[英特尔在他们的 SDK](https://software.intel.com/en-us/openclsdk-devguide-debugging-opencl-kernels-on-gpu) 中有调试器。对于 NVIDIA 卡，你可以试试他们的 [Nsight 软件](https://developer.nvidia.com/nsight-visual-studio-edition)，但是我不完全确定它是否可靠，而且他们的不是我能找到的 linux 版本

# 以图表形式采样

下图是示例使用的 API 的粗略分类。不要觉得你必须完全理解这个图，因为它只是试图给你一个各种 API 调用如何映射到一个实际程序的想法。

[![API breakdown of the sample program calls to OpenCL](img/88e4058b9d02b5ba9bc44f1311313b90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1k53AxFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/bX8nNhQ.png)

您可以在 [OpenCL API 参考页面](https://www.khronos.org/registry/OpenCL//sdk/2.0/docs/man/xhtml/)中找到 OpenCL API 调用的所有详细信息。

# TL；速度三角形定位法(dead reckoning)

OpenCL 是 Khronos 组的另一个标准，它支持设备无关的并行计算方法。有多种方法可以做到这一点，你应该采取的第一种方法是获取一个数字列表，你的计算内核，然后把它交给设备去做。这些设备可以做一些奇特的事情来使它正常工作，更多的细节将在以后的文章中公布！

# 参考文献

*   Khronos OpenCL 注册表 Khronos Group Inc. 2019。Khronos OpenCL 注册表-Khronos 集团公司[在线]可在:[https://www.khronos.org/registry/OpenCL/](https://www.khronos.org/registry/OpenCL/)获得。[访问日期:2019 年 7 月 21 日]。

*   Khronos OpenCL 20 API 规范 Khronos Group Inc. 2019。[在线]可从以下网址获取:[https://www . khronos . org/registry/OpenCL/specs/OpenCL-2.0 . pdf](https://www.khronos.org/registry/OpenCL/specs/opencl-2.0.pdf)。[访问日期:2019 年 7 月 21 日]。