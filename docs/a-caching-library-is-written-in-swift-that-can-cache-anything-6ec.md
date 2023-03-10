# 一个缓存库，用 Swift 写的，可以缓存任何东西。

> 原文：<https://dev.to/sadmansamee/a-caching-library-is-written-in-swift-that-can-cache-anything-6ec>

需要一个具有以下功能的缓存库

*   [x]异步数据下载和缓存。
*   [x]异步图像下载、缓存和显示。
*   [x]所有对象各自的到期日期/时间。
*   [x]适用于内存和磁盘的多层混合高速缓存。
*   [x]对缓存行为的精细控制。可定制的到期日期和大小限制。
*   [x]如果需要，强制刷新。
*   [x]独立组件。根据需要分别使用 [Cachy](https://github.com/Sadmansamee/CachyKit) 或 [CachyLoader](https://github.com/Sadmansamee/CachyKit) 系统。
*   [x]可以保存 JSON，UIImage，ZIP 或者任何东西。
*   [x]查看`UIImageView`的扩展。
*   加载图像时的[x]指示器。

找不到任何一体化的解决方案，不是太多就是太少，所以想出了这个。

[https://github.com/Sadmansamee/CachyKit](https://github.com/Sadmansamee/CachyKit)

这是第一个版本，可能会有很多问题或不好的地方，请指出来，我可以如何改善它们。