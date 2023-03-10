# 概观

> 原文：<https://dev.to/s_p/overview-1hna>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ xiph ](https://github.com/xiph) / [ rav1e](https://github.com/xiph/rav1e)

### 最快最安全的 AV1 编码器。

<article class="markdown-body entry-content container-lg" itemprop="text">

# rav1e[![Travis Build Status](img/5fc72a2c877cc1a35b9963ab16ebbd68.png)](https://travis-ci.org/xiph/rav1e)[![Actions Status](img/3c3b7dc471289e91c2786c2c6987300f.png)](https://github.com/xiph/rav1e/actions)[![Coverage Status](img/4ce63f8e20506708fdd8b76538563e84.png)](https://coveralls.io/github/xiph/rav1e?branch=master)

最快最安全的 AV1 编码器。

<details><summary>**Table of Content**</summary>

*   [概述](https://raw.githubusercontent.com/xiph/rav1e/master/#overview)
*   [特性](https://raw.githubusercontent.com/xiph/rav1e/master/#features)
*   [文档](https://raw.githubusercontent.com/xiph/rav1e/master/#documentation)
*   [发布](https://raw.githubusercontent.com/xiph/rav1e/master/#releases)
*   [大楼](https://raw.githubusercontent.com/xiph/rav1e/master/#building)
    *   [属地:NASM](https://raw.githubusercontent.com/xiph/rav1e/master/#dependency-nasm)
    *   [释放二进制](https://raw.githubusercontent.com/xiph/rav1e/master/#release-binary)
    *   [不稳定特征](https://raw.githubusercontent.com/xiph/rav1e/master/#unstable-features)
    *   [针对特定目标的构建](https://raw.githubusercontent.com/xiph/rav1e/master/#target-specific-builds)
    *   [构建 C-API](https://raw.githubusercontent.com/xiph/rav1e/master/#building-the-c-api)
*   [用途](https://raw.githubusercontent.com/xiph/rav1e/master/#usage)
    *   [压缩视频](https://raw.githubusercontent.com/xiph/rav1e/master/#compressing-video)
    *   [解压视频](https://raw.githubusercontent.com/xiph/rav1e/master/#decompressing-video)
    *   [配置](https://raw.githubusercontent.com/xiph/rav1e/master/#configuring)
        *   [特性](https://raw.githubusercontent.com/xiph/rav1e/master/#features-1)
*   [投稿](https://raw.githubusercontent.com/xiph/rav1e/master/#contributing)
*   [取得联系](https://raw.githubusercontent.com/xiph/rav1e/master/#getting-in-touch)</details> 

## 概观

rav1e 是 AV1 视频编码器。它被设计为最终覆盖所有用例，尽管以其当前的形式，它最适合 libaom(参考编码器)太慢的情况。

## 特征

*   帧内、帧间和交换帧
*   64x64 超级块
*   4x4 到 64x64 RDO 选择的正方形和 2:1/1:2 矩形块
*   DC、H、V、Paeth、平滑和所有方向预测模式
*   DCT、(翻转)ADST 和恒等变换(分别高达 64x64、16x16 和 32x32)
*   8 位、10 位和 12 位深度颜色
*   4:2:0(完全支持)、4:2:2 和 4:4:4(有限)色度采样
*   11 种速度设置(0-10)
*   高速水平的近实时编码
*   恒定量化器和目标比特率(单程和多程)编码模式
*   静止图像模式

…</article>

[View on GitHub](https://github.com/xiph/rav1e)
If you want to know what rav1e is, this is a good place to start.
[![luzero image](img/01c91be02992b3131e80240c141b26b8.png)](/luzero) [## 从您自己的代码中使用 rav1e

### 卢卡巴尔巴托 1919 年 4 月 9 日 17 分钟阅读

#rust #rav1e #av1](/luzero/using-rav1e-from-your-own-code-2ie0)

## 什么是？

*   码率:编码器比特流输出的码率。
*   失真:偏离原始图像。
*   RD 成本:率失真成本是比特率和质量损失之间的权衡成本。(我们希望优化这种权衡)
*   λ:用于优化研发成本的拉格朗日乘数。
*   tx: [变换](https://en.wikipedia.org/wiki/Transform_theory)
*   CDEF: [约束方向增强滤波器](https://people.xiph.org/~xiphmont/demo/av1/demo2.shtml)
*   LRF:环路恢复滤波器

## 为什么要率失真优化？

rav1e 利用这些 RDO 技术来达到压缩和质量之间权衡的鞍点。

* * *

# 机制

在所有工具和组合中彻底搜索最佳研发成本是不可行的，因为成本随着工具添加到搜索中的每个维度呈指数增长。
许多工具根据速度设置进行修剪，有些工具，如运动搜索，默认情况下是近似的。
但是在最慢的速度设置下，rav1e 中几乎所有可用的工具中都有搜索。

* * *

## 如何计算 RD 成本？

在编码过程中，我们使用失真来衡量研发成本。

```
cost = distortion + lambda * rate 
```

在后处理过滤阶段，我们使用误差来衡量研发成本。

```
cost = error + lambda * rate 
```

* * *

## 我们在为最低研发成本优化什么？

`rdo_mode_decision`:

*   帧间模式
*   内部复合模式
*   Tx 类型
*   Tx 大小
*   内部模式

`luma_chroma_mode_rdo`:

*   预测亮度的色度模式 RDO

`rdo_partition_decision`:

*   划分决策

`rdo_loop_decision`:

*   CDEF + LRF 组合

* * *

## 平铺编码概述

### 描述

每个图块独立于其他图块进行编码。我们可以在编码过程中大量观察到 RD 优化。甚至后处理滤波器的选择也考虑到了研发成本。

### 步骤

```
1\. Build coarse motion vectors of the tile for INTER blocks only.
2\. For each superblock in tile:
    1\. Motion estimation: Using either Diamond search or Full search on subsampled blocks.
    2\. encode_partition_bottomup / encode_partition_topdown. 
    3\. rdo_loop_decision.
3\. CDEF and LRF. 
```

<figure>

[![Illustration](img/c4e3f695e419dffa4b756a5e0a3545cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m9ZcmKTn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8txjqdsm4e2pdufq3hqh.png)

<figcaption>An overview of how a tile is encoded in rav1e</figcaption>

</figure>

* * *

### 接下来...

*   RDO 模式
*   分区期间的 RDO

* * *