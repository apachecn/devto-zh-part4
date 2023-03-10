# 分布式系统:CAP 定理

> 原文：<https://dev.to/tttaaannnggg/distributed-systems-the-cap-theorem-2p8a>

所以，我要开始谈论分布式系统，当我学习它们的时候。我从 CAP 定理开始，这似乎是设计分布式数据存储(带分片的数据库)的核心考虑因素。

这主要是对其他资料的总结，尤其是科达·黑尔的精彩文章

CAP 的三个部分如下:

*   **一致性:**每次我们试图从数据库中读取数据时，要么得到最新的数据，要么什么也没有。(这不同于 ACID 一致性，后者是关于维护与模式/表结构的一致性)
*   **可用性:**请求接收数据作为响应(几乎没有任何关系，不能保证最新的数据会被发送出去)
*   **分区容差:**这基本上意味着尽管消息被网络丢弃、截断、延迟或以其他方式损坏，它仍能在网络/ can 功能上工作

本质上，我们将**分区容忍度**视为给定——如果不依赖于网络，我们不可能真正拥有分布式数据存储，如果我们放弃这个，它肯定会变得不分布式。人们会不小心切断电线，或者发生紧急断电，或者电网瘫痪。如果不考虑这些问题，我们根本不可能拥有一个分布式系统。

因此，选择变成了这样:当网络关闭我们的一个节点时，我们是否仍然接受请求，发回部分(或不完全最新的)数据？或者，我们是等待一切恢复在线，协调可能同时发生的任何写入，直到我们知道一切就绪，才发送数据？

当然，这并不是板上钉钉的事。石破天惊者对 CAP 做出了断言，坦率地说，我没有资格谈论它。他似乎担心可用性不是真正的*问题，单个节点故障可以在保持一致性的同时发生，而不会中断可用性。*

> 总结我的第一点，CAP 定理假设可靠的应用程序、可靠的 DBMS、可靠的人和资源没有变化。不幸的是，这些都是必须考虑的问题。此外，将节点故障建模为网络分区会导致一个明显不好的结论。

对谁来说很明显？可能有人比我更精通 DBs。不管怎样，Stonebreaker 最后澄清说,“放弃”consist.ency 没有多大意义，因为一个足够糟糕的网络无论如何都会扼杀你的应用程序。不是基础设施故障，而是人为错误、不良因素和故障代码是真正的、频繁的停机时间来源。

然而，黑尔用一些故事来回应:

> 多节点故障可能比单节点故障更罕见，但它们仍然很常见，足以对业务产生严重影响。在我有限的经验中，我处理过单个数据中心(DC)中的长期网络分区、PDU 故障、交换机故障、整个机架的意外电源循环、整个 DC 的主干网故障、整个 DC 的电源故障，以及一名低血糖的司机将他的福特皮卡撞进了 DC 的 HVAC 系统。我甚至不是行动组的人。

最后，我确实有点倾向于 Hale 的观点，它本质上是这样的:一致性通常有容忍度，最终的一致性在很多时候是足够好的。失去可用性意味着损失金钱，这最终决定了大多数系统的需求，所以这是我们需要优先考虑的。

(顺便说一句，我也很喜欢分布式系统的名字——[cocroach db](https://www.cockroachlabs.com/)和 [Project Voldemort](https://www.project-voldemort.com/voldemort/) 对我来说是特别有趣的名字)

延伸阅读:
T1】https://www . Microsoft . com/en-us/research/WP-content/uploads/2011/10/consistencyandbaseballreport . pdf
[https://coda hale . com/you-cant-sacrifice-partition-tolerance/](https://codahale.com/you-cant-sacrifice-partition-tolerance/)
[https://Martin . kleppmann . com/2015/05/11/please-stop-calling-databases-CP-or-AP . html](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)