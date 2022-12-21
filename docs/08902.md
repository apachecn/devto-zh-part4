# Java 堆的自动垂直伸缩

> 原文：<https://dev.to/educostadev/automatic-vertical-scaling-of-the-java-heap-5eml>

多年来，java 是贪婪应用程序的同义词，那种在晚上打开冰箱，吃掉所有可用资源直到崩溃的应用程序。这种行为的主要原因是缺乏一种有效的方法来将分配在 java 堆中且不再使用的内存归还给操作系统。

然而，通过 OpenJDK 12 上发布的新特性，结合 [Shenandoah 垃圾收集器算法](https://wiki.openjdk.java.net/display/shenandoah/Main)，JVM 现在能够动态缩放 java 堆，并在必要时将提交的内存交还给 SO。这是一个令人惊叹的特性，当您在云环境中运行微服务应用程序时，它可以让您节省资金，而云环境通常是按使用的资源收费的。

查看此[视频](https://www.youtube.com/watch?v=_NOOljpSM08)(幻灯片[此处](https://www.slideshare.net/jelastic/elastic-jvm-automatic-vertical-scaling-of-the-java-heap?ref=))

这篇文章最初发表在我的博客上。如果你有任何疑问或问题，欢迎在这里发表评论或通过 Twitter [@educostadev](http://twitter.com/educostadev) 联系我。