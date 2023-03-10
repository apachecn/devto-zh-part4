# TPP 主题 11:可逆性

> 原文：<https://dev.to/steadbytes/tpp-topic-11-reversibility-3m71>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-11-challenges/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 是时候用薛定谔的猫来研究一下量子力学了。假设你有一只猫在一个封闭的盒子里，还有一个放射性粒子。这个粒子有 50%的几率分裂成两个。如果是的话，那只猫就会被杀死。如果没有，猫就没事了。那么，猫是死是活呢？根据薛定谔的说法，正确的答案是两者都有(至少在盒子保持关闭的时候)。每当有两种可能结果的亚核反应发生时，宇宙就被克隆了。在一种情况下，事件发生了，在另一种情况下，事件没有发生。猫在一个宇宙中活着，在另一个宇宙中死去。只有打开盒子，你才知道自己身处哪个宇宙。
> 
> 难怪为未来编码很难。
> 
> 但是把代码进化想象成一个装满薛定谔的猫的盒子:每个决定都会导致未来的不同版本。你的代码能支持多少种可能的未来？哪些可能性更大？到时候养活他们会有多难？你敢打开盒子吗？

这一挑战是对现有代码库进行的一次伟大的思想实验，尽管由于某些决策的可逆性与上下文高度相关，因此很难得出概括性的结论。然而，我将对我考虑过的一些观点进行讨论(有很多)。

我猜想作者使用了相当模糊的短语“可能的未来”来迫使读者考虑软件项目未来可能发生变化的一些方式。一些高级建议:

*   技术趋势/创新
    *   即编程语言、框架、云计算等。
*   行业/业务领域趋势/创新
    *   即在 2000 年代中期引入网上杂货店购物
*   不断发展的业务需求
    *   即扩展到新的地理位置、进入新产品市场、瞄准不同的客户、完成产品转移、收购等

### 编程语言

一般来说，大多数广泛使用的**编程语言都有能力支持许多‘可能的未来’。它们是一种*工具*，可以用来建造许多不同的解决方案——锤子可以用来建造棚子或船。当然，有些语言更适合某些任务，有些可能*实际上*不可能使用——C #可能比 Java 更适合基于 Windows 的应用程序，但两者都可以，而用 Erlang 构建 iOS 应用程序可能不切实际。**

### 软件框架

与编程语言相比，框架为更具体的领域提供功能，因此它们不太经得起未来的考验。技术趋势/创新或业务方向的巨大转变可能会迫使框架发生变化，但是通过改变现有功能和添加新功能来实现发展通常不是问题。Django 非常适合 web 应用程序，但是将产品转移到桌面或移动设备将需要一个完全不同的框架(尽管 Django 可以用作这类应用程序的后端)。此外，在一个*设计良好的*软件项目中，核心业务逻辑应该从框架中分离出来——允许框架的选择被颠倒并替换为不同的框架。

### 数据库

通常作为软件的核心，数据库很难更改，原因有很多:

*   糟糕的设计可能会导致数据库查询分散在应用程序的整个业务逻辑中
*   完全不同的数据模型可能需要更改从数据库中检索哪些值以及如何使用它们

    *   SQL vs NoSQL vs 图形
*   将数据从一个数据库导出/导入到另一个数据库

也就是说，改变数据库的动机(除了 CV 驱动的开发)通常来自于一个新的特性，这个新的特性独立于/附加于现有的应用程序，因此通常可以与现有的数据库一起使用。例如， [Elasticsearch](https://www.elastic.co/products/elasticsearch) 可以用来在现有的基于 SQL 的应用程序上提供一个强大的搜索引擎。