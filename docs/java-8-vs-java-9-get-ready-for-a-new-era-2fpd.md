# Java 8 与 Java 9:为新时代做好准备

> 原文：<https://dev.to/serhiykozlov/java-8-vs-java-9-get-ready-for-a-new-era-2fpd>

普通企业主只希望自己的软件或应用程序能够稳定运行。他要么有内部开发人员，要么外包出去。无论哪种方式，他决定他想要一个新软件做什么，传达给他的开发人员，并期望他们通过。他有权期待的是一个专门为他的公司设计的定制解决方案，通过一些培训和支持，将满足他对性能的期望。

这通常意味着开发人员转向 Java，一种具有惊人多功能性的编程语言。今天全球有 [超过 650 万的 Java 开发者](https://www.thoughtco.com/what-is-java-2034117) ，因为它的实用性、易用性、安全性、可靠性，以及平台独立性(无论使用什么硬件或操作系统都能工作)。

## 为什么是 Java 和 Java 9？

[![why Java](img/987761467c32f6b0535cf038822b92b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0AnvtU2s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.romexsoft.com/wp-content/uploads/2017/04/882x200-12.jpg)

Java 由 Sun Microsystems 公司于 1995 年开发，最初是为交互式电视设计的。然而，当 Java 1.0 公开发布时，它已经将焦点转移到了互联网的使用上。对于上面列出的所有元素，它成为最流行的编程语言之一。

显然，从 1.0 开始已经有了很多更新。最新版本是 Java 8，正在被 Java 9 取代的过程中。甲骨文宣布 [Java 9 发布日期2016 年 9 月 22 日](http://www.networkworld.com/article/2920183/microsoft-subnet/mark-your-calendar-java-9-finally-has-a-release-date.html) ，在接下来的 16 个月里陆续推出新的特性和元素。出于多种原因，该日期已被推迟到 2017 年 3 月，现在又被推迟到 7 月，但主要是因为开发人员希望花费额外的时间来确保在发布之前修复 bug 和问题——这是 Java 8 发布时没有做到的。

Java 9 姗姗来迟，原因如下。当甲骨文在 2010 年收购 Sun Microsystems 时，Java 在漫长的收购过程中被搁置了。它失去了很多人才。甲骨文不得不重建一个 Java 开发团队，然后开始了修改和渴望已久的模块化过程。

Java 9 的许多特性让开发人员非常兴奋。这些新特性将对需要快速、高效和健壮的定制软件解决方案的企业产生重要影响。

### Java 9 的新特性:快速总结

虽然这些对企业主来说可能意义不大，但 Java 9 的新特性对开发人员来说意义重大，因为他们关注的是新特性。下面是四个最重要的概述。

1.  [**Jshell**](http://openjdk.java.net/jeps/222) :这是一个新的命令行工具。这意味着，如果开发人员想单独运行几行 Java，不需要将它们都打包在一个单独的方法或项目中就可以完成。
2.  [**微基准**](http://openjdk.java.net/jeps/230) **:** 现在，非常具体的小块代码的性能可以用标准化的方法来衡量。用于分析低至纳秒的 Java harness (JMH)是 Java 9 独有的。
3.  [**全面支持 HTTP 2.0 客户端**](http://openjdk.java.net/jeps/110) :这是速度的事情，HTTP 2.0 客户端已经表现出提供更快的速度， [比 HTTP 1.1 客户端](http://blog.takipi.com/5-features-in-java-9-that-will-change-how-you-develop-software-and-2-that-wont/) 提升 11.81%到 47.7%不等。
4.  [**进程 API**](http://openjdk.java.net/jeps/102) :一个 API(应用程序编程接口)允许 app 的进程之间不用使用原生代码就可以相互通信。Java 9 的这次更新将扩展 Java 控制和管理操作系统进程的能力。
5.  [**模块化源代码**](http://openjdk.java.net/jeps/201) :将 JDK 源代码组织成模块。

听起来很有希望，不是吗？

> *From rational perspective the most awaited feature for me is the new HTTP Client that supports both HTTP/2 protocol and WebSocket. And here’s why:*
> 
> 1.  *非阻塞模式支持(每个请求/响应多个线程)。T3】*
> 2.  *增强性能 (与 HTTP/1.1 相比提高 4 5%)使 与 Netty 或 Jetty 不相上下。T9】*
> 3.  *服务器推送支持，无需明确请求即可将资源推送到客户端。T3】*
> 
> *The second feature (less important though the cute one) I’m looking forward to is the Streaming API improvement, especially the dropWhile and takeWhile methods that will be very useful in functional programming.*
> 
> - Ivan，Romexsoft 高级 Java 开发人员

### Java 8 和 Java 9 的对比

Java 8 的特性赋予了开发者非凡的多功能性，能够为各种各样的商业领域提供软件解决方案，包括 [金融科技](https://www.romexsoft.com/fintech/)[医疗保健](https://www.romexsoft.com/healthcare-app-development/) 以及其他行业。
[![](img/a9eb4bd0ab46c4dd2c9a7e6d21668b87.png)](https://www.romexsoft.com/contact-us/)
然而，Java 9 的到来承诺提供更多的多功能性和进一步定制的能力。对于企业来说，这是一个巨大的优势。

这里，用相当简单的话来说，只是 Java 9 的一些新特性，Java 8 没有。

[**标题“竖锯”**的原因](http://openjdk.java.net/projects/jigsaw/)

Jigsaw 项目是 Java 的模块化。基本上，这意味着编程代码被分解并根据模块执行的任务组织成模块。通过这样做，模块可以重用，并且更容易管理和调试。这意味着软件开发变得更容易，更精简，调试更快。这是 Java 8 和 Java 9 的一个关键区别。

第二大好处——它使 Java 平台更加轻量级和可伸缩性更强。它允许 Java 应用程序甚至在低生产率的设备上运行(需要更少的工作资源)。

> *The latest Java 8 release brought a variety of new features that significantly changed developer's life. But what's more, Oracle prepares to release Java 9 with a bunch of new groundbreaking ones.* *For me, the most anticipated feature of Java 9 is Jigsaw project which introduces a new module system that breaks the JRE into interoperable components.* *With a modular JRE, applications will be enabled to run on even embedded systems. This is an especially huge change in the context of the advent of Internet of Things (IoT). It carries forward Java and it means we have a chance to have a big number of IoT apps written in Java.*
> 
> - Yura，Romexsoft 高级全栈 Java 开发人员

[**Jshell 命令行工具**](http://openjdk.java.net/jeps/222)

如上所述，这是一个交互式工具，它允许测试代码的小片段，而不需要创建一个新的类。这个新的 shell 还具有历史和自动完成特性以及其他功能，比如保存和加载一些或所有的书面陈述。

[**多分辨率图像**](http://openjdk.java.net/jeps/251)

这个 API 将允许把一组不同分辨率的图像封装成一个单独的对象。因此，开发人员可以检索分辨率特定的图像，或者检索图像中的所有变体。

[**统一 JVM 日志**](http://openjdk.java.net/jeps/158)

现在，所有 JVM 组件都有一个通用的日志系统。然而，如果我们看看 Java 9 的优点和缺点，这里有一个。它没有从所有 JVM 组件添加日志调用，也没有在 Java 开发工具包中向 Java 代码添加日志。

[**发布-订阅框架**](http://openjdk.java.net/jeps/266)

这是对 Java 8 的改进，因为这些接口将更好地支持运行在 JVM(Java 虚拟机)上的许多异步系统的互操作性。JVM 背后的想法是，通过将 Java 代码转换成机器语言，然后执行它，它们允许任何计算机运行 Java 程序。这方面的改进是 Java 9 的一大优势。

**不可变的列表、集合、映射和映射。入口 API**

在 Java 8 中，要创建几个元素的集合，开发者必须创建几行代码。现在只需一行代码就可以完成。Java 8 耗时的缺点之一就这样被消除了。

> *I’m expecting the Stream API Improvements. Why? It will facilitate the work with arrays and accelerate various functions.* *As an example, check this:* *Stream.of(1,2,3,4,5,6,7,8,9,10).takeWhile(i -> i < 5 ).forEach(System.out::println);* *Will deliver: 1 2 3 4* *Looks promising, isn't it?*
> 
> - Mykhaylo，Romexsoft 团队负责人

### Java 9 给企业带来的好处

诚然，对于寻找最佳软件解决方案来满足其需求的企业来说，Java 8 与 Java 9 的细节可能不是很感兴趣。然而，对于开发人员来说，在创建这些软件解决方案时，Java 8 和 Java 9 的性能会有很大的不同。

无论使用 Java SE 9 还是任何其他版本，根据业务需要，最新功能的引入将带来以下改进:

*   更快的开发速度，特别是由于模块系统可重复使用，更易于调试和管理。
*   模块系统的另一个好处是，它将提高应用程序(小的或大的)的资源效率，因为对于它们的开发，程序员将只需要取需要的模块，而不是所有的 JRE。
*   使用单行而不是几行代码创建集合。
*   允许对非常小的代码片段进行性能分析的微基准测试。
*   支持 HTTP 2.0 客户端，速度更快。

这只是部分列表。但是所有这些和其他新特性使得开发人员设计定制业务解决方案的路线图，然后基于该路线图创建软件的工作更加简化。开发、测试和调试的流程化意味着最终产品需要解决的问题会少得多。在这种类型的开发环境中，每个人都是赢家。

如果您正在寻找软件解决方案开发方面的专业知识，我们邀请您联系 Romexsoft，提出您的需求，并允许我们向您概述我们如何通过我们当前的 Java 8 专业知识和即将推出的 Java 9 特性来满足您的需求。我们已经准备好迎接这个 Java 的新时代了！
*作者:尤拉·邦达伦科*
原载于 Romexsoft 博客[“Java 8 vs . Java 9:为新时代做好准备”](https://www.romexsoft.com/blog/java-8-vs-java-9/)