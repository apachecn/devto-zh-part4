# Direct3D 12 和 Vulkan 之间内存管理的差异

> 原文：<https://dev.to/reg__/differences-in-memory-management-between-direct3d-12-and-vulkan-5b84>

自 2017 年 7 月以来，我开发了 [Vulkan 内存分配器](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/)(VMA)——一个 C++库，帮助使用 Vulkan 的游戏和其他应用程序进行内存管理。但是因为我在日常工作中同时与 Vulkan 和 DirectX 12 打交道，所以我认为比较它们是个好主意。

这是一篇关于一个非常具体的话题的文章。如果您是同时使用 Direct3D 12 和 Vulkan 这两种图形 API 的程序员，它可能会对您有用。这两个 API 提供了一组相似的特性和性能。两者都是现代图形硬件(GPU)的新一代、显式、低级接口，因此我们可以连续比较它们以显示相似性和差异，例如在命名事物方面。例如，`ID3D12CommandQueue::ExecuteCommandLists`函数在`vkQueueSubmit`函数的形式上有 Vulkan 等价。然而，本文只关注一个方面——内存管理，这意味着 GPU 内存分配和资源创建的规则和限制——图像(纹理、渲染目标、深度模板表面等。)和缓冲器(顶点缓冲器、索引缓冲器、常数/均匀缓冲器等。)下面的章节描述了这两个 API 之间内存管理的几乎所有不同方面。

[**阅读全文**](http://asawicki.info/articles/memory_management_vulkan_direct3d_12.php5)