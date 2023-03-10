# 如何修复 Spring Boot 应用程序中的“Mockito 不能模仿这个类”异常

> 原文：<https://dev.to/scottshipp/how-to-fix-a-mockito-cannot-mock-this-class-exception-in-a-spring-boot-app-457e>

最近，我用 2.0.5 版和 Java 11 开发了一个新的 Spring Boot 应用程序。起初，一切似乎都没什么大不了的，我经历了一些引导阶段，让一个行走的骨架运行起来，用 Maven 构建 Docker 容器，甚至将快照版本及其 Docker 容器推送到 AWS ECR。

然后我写了第一个控制器，出乎意料的是，我无法为它写集成测试(@WebMvcTest)。当我尝试的时候，我得到了下面的错误:

```
[ERROR] testGetGear(org.gearbuddy.GearControllerTest)  Time elapsed: 0 s  < << ERROR!
java.lang.IllegalStateException: Failed to load ApplicationContext
Caused by: org.mockito.exceptions.base.MockitoException: 

Mockito cannot mock this class: interface org.gearbuddy.data.GearRepository.

Mockito can only mock non-private & non-final classes.
If you're not sure why you're getting this error, please report to the mailing list.

Java               : 11
JVM vendor name    : Oracle Corporation
JVM vendor version : 11.0.2+9-Ubuntu-3ubuntu118.04.3
JVM name           : OpenJDK 64-Bit Server VM
JVM version        : 11.0.2+9-Ubuntu-3ubuntu118.04.3
JVM info           : mixed mode, sharing
OS name            : Linux
OS version         : 4.15.0-48-generic

Underlying exception : java.lang.UnsupportedOperationException: Cannot define class using reflection
Caused by: java.lang.UnsupportedOperationException: Cannot define class using reflection
Caused by: java.lang.IllegalStateException: Could not find sun.misc.Unsafe
Caused by: java.lang.NoSuchMethodException: sun.misc.Unsafe.defineClass(java.lang.String, [B, int, int, java.lang.ClassLoader, java.security.ProtectionDomain) 
```

Enter fullscreen mode Exit fullscreen mode

哼，太奇怪了！

这是它无法模仿的代码:

```
public interface GearRepository extends CrudRepository {
} 
```

Enter fullscreen mode Exit fullscreen mode

没什么特别的！

我是怎么修好的？

[![Tools lying on a workbench](img/9e49e597c45d589a9f62d5fed87b3ba7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ooz0UiP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ig6sol1dycg3hot03gkt.jpg)

### 解

解决方案只是显式地在我的 POM 中添加一个新版本的 mock ITO:

```
<!-- https://mvnrepository.com/artifact/org.mockito/mockito-core -->
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.22.0</version>
    <scope>test</scope>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

在此之前，我有 Mockito 2.15.0，它是通过 spring-boot-starter-test 作为一个可传递的依赖项出现的。

那么，为什么添加显式 Mockito 版本有效呢？

嗯，问题是 Mockito 版本中的 MockBean 是作为传递依赖导入的(我觉得是从 Spring Boot 父 pom(？)但我没有检查)用过的东西来自 sun.misc.unsafe 包。[在 Java 9](https://www.oracle.com/technetwork/java/javase/9-removed-features-3745614.html) 中被移除。

不幸的是，我花了几个小时才弄明白这是怎么回事。因为如果你开始搜索“Mockito 不能模仿这个类 CrudRepository ”,你会找到很多关于 Spring Boot 如何创建应用程序上下文(特别是关于@WebMvcTest 注释)、beans 何时可用以及它们可用时的名称等等的文章。

另一方面，如果你查找类似“Mockito java 11 sun.misc.unsafe”的内容(当你一直读到上面的堆栈跟踪结束时，你最终会尝试这样做)，你会遇到[这个非常有用的堆栈溢出答案](https://stackoverflow.com/questions/52533878/mockito-error-in-spring-boot-tests-after-migrating-to-jdk-11)。

新的更快的 Java 升级周期可能会给应用程序带来一些类似这样的依赖性问题。谢天谢地，在线 Java 社区是一个强大而有用的社区。只要稍微仔细阅读一下任何错误输出，就可以毫不费力地找到您需要的解决方案。