# 一个小时的时间不算太糟

> 原文：<https://dev.to/azure/not-too-bad-for-an-hour-s-time-43fj>

孔雀在过去的几个月里成长了不少。事实上，README.md 中的文档越来越长，很难浏览。

我决定开发一个静态站点生成器，我可以用它来显示文档，让大家更容易使用它们。

以下是我的主要目标:

*   对现有降价的最小改变
*   迁移所有图像
*   添加贡献、变更日志和其他有用项目的附加链接
*   与 peacock code 分开，在 docs 文件夹中
*   自定义域中的云中的主机
*   搜索引擎友好
*   删除旧的降价
*   保留原始自述文件，并决定哪些内容应该保留在那里，哪些内容应该链接到文档
*   维护代码和其他降价样式
*   自定义主题

我决定这是一个尝试 VuePress 的好机会。

哦，我从来没有用过 VuePress！所以我去了 VuePress docs 网站开始研究。一小时后，这里就是我着陆的地方。

[![](img/b43059ca6c21a85f66101a403b313051.png)](https://i.giphy.com/media/SX0HDuBWB4nm09vJZD/giphy.gif)

接下来还会有更多，但我很高兴我可以做到所有这些，而不用编写任何 Vue/React/Angular 代码！我喜欢这些工具，我也喜欢 Vue 是 VuePress 的核心。为什么？因为这意味着当我需要定制时，我可以直接使用 Vue 组件。但是简单地把我的 markdown 做成了一个 docs 网站，就简单多了。不需要 Vue 知识！

稍后我会写更多，但我想分享我是如何快速推进 VuePress 的。如果你一直在考虑一个文档工具，一定要试一试。

到了托管它的时候，我可能会考虑 [Azure Storage](https://docs.microsoft.com/en-us/azure/storage/?wt.mc_id=devto-blog-jopapa) 或者 GitHub pages。还没准备好主办，但我很高兴我已经能想到那么远了😊

感谢[克里斯·诺林](//htttps://twitter.com/chris_noring)与我配对！