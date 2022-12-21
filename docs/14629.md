# 六边形建筑-下一代

> 原文：<https://dev.to/bertilmuth/hexagonal-architecture-next-generation-5e6h>

最近，我写了很多关于[实现六边形架构](https://dev.to/bertilmuth/implementing-a-hexagonal-architecture-1kgf)的文章。

作为反馈出现的一个问题是应用程序使用了共享的可变状态。它建立了一个会话 bean，这样[驱动适配器](https://github.com/bertilmuth/poem-springboot/blob/master/src/main/java/poem/springboot/driver_adapter/SpringMvcDriver.java)可以联系[驱动适配器](https://github.com/bertilmuth/poem-springboot/blob/master/src/main/java/poem/springboot/driven_adapter/SpringMvcPublisher.java)将结果数据添加到 Spring 的 MVC 模型中。共享可变状态和阻塞操作在生产环境中可能是一个障碍。

我只是想告诉你，我创造了一个新版本的样本六角形核心，和 Spring Boot 适配器。应用程序:

*   让我们来配置你是否想把诗数据返回给驱动适配器(例如 Spring 控制器)，或者把它传递给从动适配器(例如[控制台写入器](https://github.com/bertilmuth/poem-hexagon-nextgen/blob/master/src/main/java/poem/simple/driven_adapter/ConsoleWriter.java))
*   以异步方式访问六边形[，但允许您等待结果](https://github.com/bertilmuth/poem-hexagon-nextgen/blob/master/src/main/java/poem/boundary/Boundary.java)
*   避免共享可变状态

你在这个项目中找到六边形核心:
[https://github.com/bertilmuth/poem-hexagon-nextgen](https://github.com/bertilmuth/poem-hexagon-nextgen)

你在这个项目中找到 Spring Boot 适配器:
[https://github.com/bertilmuth/poem-springboot-nextgen](https://github.com/bertilmuth/poem-springboot-nextgen)

我知道这只是一个简短的总结。如果你有任何问题或者需要更多的信息，请告诉我。

# 鸣谢

威利·舍恩博恩和 T2·胡安姆格普的耐心和想法值得称赞。