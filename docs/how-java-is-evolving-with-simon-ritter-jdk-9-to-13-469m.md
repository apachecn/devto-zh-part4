# 西蒙·里特- JDK 9 到 13 Java 是如何发展的

> 原文：<https://dev.to/educostadev/how-java-is-evolving-with-simon-ritter-jdk-9-to-13-469m>

在这篇文章中，我将分享我对 Simon Ritter 的一次精彩演讲的笔记，题目是“从 9 岁到 13 岁的 JDK，Java 是如何与 Simon Ritter 一起发展的”。Simon Ritter 是 Azul Systems 的副首席技术官，在该领域工作了很长时间。该演讲由 Java 用户群 Campinas T1 组织，由巴西圣保罗州 campi nas 市的 T2 Movile T3 和 T4 Wavy T5 公司赞助。下面的笔记总结了我对讲座主题的理解，所以请让我知道我是否有任何误解。

Simon 概述了 java 是如何发展的，以及自 JDK 9 以来的主要变化。Java 已经改变了它的二进制和交付策略。两年前的 JDK 9 是引入了大约 90 项新功能的最后一个主要版本，JDK 9 和 10 是中间版本，Oracle 开始清理和删除过时的功能。JDK 9 的一些特性包括:

*   [Java 平台模块系统(JPMS)](https://openjdk.java.net/projects/jigsaw/spec/) :允许对前一版本的包进行分组，最终有 26 个主模块，用于创建特定于您的应用程序的运行时。
*   [JEP 260:封装大部分内部 API](https://openjdk.java.net/jeps/260):默认情况下封装大部分 JDK 的内部 API，以便在编译时不可访问，并为将来的版本做好准备，在将来的版本中，它们将在运行时不可访问。
*   [JEP 282:jlink:Java 链接器](https://openjdk.java.net/jeps/282):可以将一组模块及其依赖项组装和优化成定制运行时映像的工具。

每 6 个月交付新版本的新发布模型为开发带来了更多的灵活性，并允许快速采用新特性。当云提供商开始以 JDK 发布的速度发布新的 JDK 版本时，我会非常高兴。JDK 的长期支持(LTS)现在每 3 年一次，当前的 LTS 版本是 JDK 11，这意味着 Oracle 将向 JDK 11 提供修复，直到下一个 LTS 版本推出。

OpenJDK 也已经发展到与 JDK 版本一致，所以 OpenJDK 11 和 JDK 11 现在具有相同的特性，目标是两者一起发展。OpenJDK 和 JDK 之间的一个主要区别在于许可证模型，而 JDK 11 及更高版本只对开发和测试环境免费，您必须支付 Oracle Java SE 订阅费才能在生产中使用它。OpenJDK 使用 GPLv2 许可证，没有使用限制。JDK 8 仍然无限期免费，但没有官方更新，如安全补丁和错误修复。

JDK 10 推出了一些很酷的新功能，比如:

*   [JEP 286:局部变量类型推断](https://openjdk.java.net/jeps/286):增强 Java 语言，将类型推断扩展到带有初始化器的局部变量声明。
*   [JEP 307:针对 G1 的并行全 GC](https://openjdk.java.net/jeps/307):通过实现全 GC 并行化，改善 G1 最坏情况下的延迟。
*   J [EP 310:应用程序类-数据共享](https://openjdk.java.net/jeps/310):改善启动和内存占用。
*   [JEP 317:基于 Java 的实验性 JIT 编译器:](https://openjdk.java.net/jeps/317)启用基于 Java 的 JIT 编译器 Graal，作为 Linux/x64 平台上的实验性 JIT 编译器。
*   用于集合、字符串等的新 API。

JDK 11 被认为是 Oracle 赋予 Java 新方向的起点，一些新特性包括:

*   [JEP 323:Lambda 参数的局部变量语法](https://openjdk.java.net/jeps/323):允许在声明隐式类型 Lambda 表达式的形参时使用 var。
*   [JEP 330:启动单文件源代码程序](https://openjdk.java.net/jeps/330):增强 java 启动器，以运行作为单文件 Java 源代码提供的程序。
*   [JEP 181:基于嵌套的访问控制:](https://openjdk.java.net/jeps/181)引入嵌套，这是一种访问控制上下文，与现有的嵌套类型概念一致。
*   JEP 309:动态类文件常量:扩展 Java 类文件格式以支持新的常量池形式。
*   [JEP 318: Epsilon:一个无操作垃圾收集器:](https://openjdk.java.net/jeps/318)开发一个 GC，处理内存分配，但不实现任何实际的内存回收机制。
*   [JEP 321: HTTP Client(标准):](https://openjdk.java.net/jeps/321)标准化在 JDK 9 通过 JEP 110 引入并在 JDK 10 更新的孵化 HTTP Client API。
*   [JEP 333: ZGC:可扩展的低延迟垃圾收集器:](https://openjdk.java.net/jeps/333)Z 垃圾收集器，也称为 ZGC，是一种可扩展的低延迟垃圾收集器。
*   包含了一个新的谓词，not(谓词)来允许酷酷的 thinks like list.stream()。filter(not(String::isEmpty)
*   [删除 API](https://dzone.com/articles/apis-to-be-removed-from-java-11)包括与 Java EE 相关的 API，如 java.xml 和 java.xm.ws。
*   还有更多…

JDK 12 是每 6 个月交付一次的新车型中的第一款，它也有很好的功能，这是 JEP 346 的一个亮点如下:

*   [JEP 346:及时从 G1 返回未使用的提交内存](https://openjdk.java.net/jeps/346):增强 G1 垃圾收集器，在空闲时自动将 Java 堆内存返回给操作系统。
*   [JEP 325: Switch 表达式:](https://openjdk.java.net/jeps/325)扩展 Switch 语句，使其既可以用作语句，也可以用作表达式。
*   [JEP 189: Shenandoah:一个低暂停时间的垃圾收集器](https://openjdk.java.net/jeps/189):添加一个新的垃圾收集(GC)算法，名为 Shenandoah，它通过与正在运行的 Java 线程并发执行疏散工作来减少 GC 暂停时间。
*   [JEP 344:针对 G1 的可中止混合收集](https://openjdk.java.net/jeps/344):如果 G1 混合收集可能超过暂停目标，则使其可中止。
*   还有更多…

JDK 13 即将推出，以下是一些计划中的功能和已经开始的酷项目:

*   [JEP 355:文本块(预览)](https://openjdk.java.net/jeps/355):包含多行字符串文字，避免了大多数转义序列的需要，以可预测的方式自动格式化字符串，并在需要时让开发人员控制格式。
*   [JEP 354: Switch 表达式(预览)](https://openjdk.java.net/jeps/354):扩展 Switch，包含 yield 关键字。
*   [项目 Valhalla](https://openjdk.java.net/projects/valhalla/) :提供一个探索和孵化高级 Java VM 和语言特性候选者的场所。
*   Project Loom :探索和培育 Java VM 特性和基于它们构建的 API，以实现轻量级用户模式线程

最后，西蒙展示了祖鲁 JDK，这是一款为他的公司制造的 JDK，为那些希望继续使用 JDK 8 并在甲骨文停止更新后接收安全更新的公司提供解决方案。

### 结论

这是一次很棒的演讲，Simon 掌握了主题，并以一种对象的方式展示了引入的主要特性，以及 Java 在未来几年的发展将会有多敏捷。交付的模式让我很兴奋，因为每 6 个月就可以在 JDK 引入一些好的特性，同时语言变得更有趣，JVM 变得更健壮。我希望云提供商能在 oracle 发布这个版本后尽快发布。另一件让我高兴的事情是 java 能够在空闲时自动将 Java 堆内存返回给操作系统，这是一个令人惊奇的特性。在这里看到我的另一个帖子[Java 堆的自动垂直缩放。](https://educosta.dev/blog/automatic-vertical-scaling-of-the-java-heap/)

这篇文章最初发表在我的博客上。如果你有任何疑问或问题，欢迎在这里发表评论或通过 Twitter [@educostadev](http://twitter.com/educostadev) 联系我。