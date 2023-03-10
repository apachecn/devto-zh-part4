# 理解遗留代码

> 原文：<https://dev.to/janux_de/understanding-legacy-code-1kdd>

> *遗产*的定义:祖先、前任或过去传递或接收的东西
> ——[韦氏词典](https://www.merriam-webster.com/dictionary/legacy)

上坡分析和下坡发明的[定律表明，从零开始创造东西总比建立在他人的工作基础上更容易。然而，就软件而言，](https://www.youtube.com/watch?v=nVRUv30coyA&feature=youtu.be&t=1893)[现实世界由棕色领域应用](https://www.youtube.com/watch?v=VdI2ca16TFk&feature=youtu.be&t=488)组成，旧系统需要添加新的部件。因此，理解遗留代码——这里定义为原始作者不再回答问题的代码——是软件开发人员生活中的一个基本部分。这篇博客文章收集了一些应对这项艰巨任务的提示。

# 有积极的态度

> 要有耐心，要坚韧；总有一天这种痛苦会对你有用。
> ——[奥维德](http://wisdomquotes.com/patience-quotes/)

同样的情况可能会被一个人视为负担，而另一个人视之为挑战。态度决定一切。

仍然处理遗留代码意味着处理挫折。因此，将[健壮性原则](https://en.wikipedia.org/wiki/Robustness_principle)不仅应用于代码，也应用于个人层面是有帮助的:“对你所做的事情保持保守，对你从他人那里接受的东西保持开放。”

不管怎样，事情越困难，我们越能从中吸取教训。

# 简化问题

模型是对现实的简化表示，它忽略了与解决手头问题无关的一切。有了这个，思考问题和找到解决方案可能会更容易。

创建软件系统模型的一种方法是在草稿纸或白板上创建划痕。找出重要事物的名称并写下来。在它们周围画圆形和矩形。在它们之间画直线和箭头。使用不同的颜色。

还有建模符号，像[统一建模语言(UML)](https://martinfowler.com/books/uml.html) 、 [C4](https://c4model.com/) 、[实体关系建模(ERM)](https://en.wikipedia.org/wiki/Entity%E2%80%93relationship_model) 、[数据流图](https://en.wikipedia.org/wiki/Data-flow_diagram)等。可以使用。当草图变得复杂时，使用像[视觉范例](https://www.visual-paradigm.com/)、[直径](http://dia-installer.de/)或 [draw.io](https://www.youtube.com/watch?v=Z0D96ZikMkc) 这样的建模工具可能会有用。

# 完善文档

学习向别人解释的最好方法之一。进一步记录下那些你很难理解的东西可能会对你和你的后来人有所帮助。

*   查看 [HTML 健全性检查器架构文档](https://hsc.aim42.org/documentation/hsc_arc42.html)中的一个很好的例子。
*   为每个子项目创建一个[自述文件](https://ymmv.craftswerk.io/2017/10/What-makes-a-good-README)。
*   为项目的领域语言创建一个词汇表。

# 成为文本编辑器的超级用户

文本编辑器的开发人员(包括 ide)花费了大量的时间和精力来帮助提高代码阅读效率。因此，学习如何使用你最喜欢的编辑器的高级功能的投资很可能会有回报。

IntelliJ IDEA 示例:

*   [源代码导航](https://www.jetbrains.com/help/idea/navigating-through-the-source-code.html)
*   [调试](https://www.jetbrains.com/help/idea/debugging-code.html)
*   [查看结构和层次](https://www.jetbrains.com/help/idea/viewing-structure-and-hierarchy-of-the-source-code.html)

# 概述代码库

就像阅读书籍一样，不同的[阅读技巧](https://www.howtolearn.com/2012/08/different-reading-techniques-and-when-to-use-them/)可以应用于阅读代码。设置一个时间框(比如一个小时)，浏览整个代码库。了解架构模式和编码风格。

可以应用以下策略:

*   自上而下:首先看 API/UI，然后是业务流程和用例。
*   自下而上:首先查看数据库表，然后逐步查看用例及业务流程。
*   [识别热点](https://dev.to/janux_de/hotspot-analysis-for-a-java-project-4ej6)，即最复杂、变化最频繁的区域。

# 通过重构来理解

> 整理的时候我们不是来修复代码的，我们是来理解代码的。— [肯特·贝克](https://twitter.com/kentbeck/status/1120811117515722752)

当代码难以理解时，使用改进的代码结构可能会变得更容易。实现这一点的技术被称为[重构](https://books.google.de/books?id=o69NtAEACAAJ):重命名变量、提取方法等。这有助于逐步建立应用程序的心理模型。

如果风险太高而无法在主代码库中应用更改，那么在获得更好的理解后，它们可能会被丢弃。

# 应用静态分析工具

在你的语言生态系统中可能有一些工具可以让你更容易理解遗留代码。以下是这类工具的一些想法:

*   代码搜索引擎，如 [Sourcetrail](https://www.sourcetrail.com/) 和 [Sourcegraph](https://about.sourcegraph.com/) 。
*   城市可视化代码，例如 [CodeCharta](https://www.maibornwolff.de/codecharta) 。
*   使用脚本语言编写自己的实用程序，例如 Python。

# 参考文献

*   [软件考古学](https://media.pragprog.com/articles/mar_02_archeology.pdf) |安迪·亨特和迪夫·托马斯

*   [有效使用遗留代码](https://books.google.com/books?id=fB6s_Z6g0gIC) | Michael Feathers

*   理解遗留代码的 5 个技巧

*   [乔纳森·博卡拉谈理解遗留代码](https://www.se-radio.net/2019/04/363-jonathan-boccara-on-understanding-legacy-code/) |软件工程无线电

*   [如何理解新代码](https://www.youtube.com/watch?v=4e7mjh83hN8) | Dave Xiang

*   [代码阅读:开源视角](https://books.google.com/books?id=vEN-ckcdtCwC) | Diomidis Spinellis