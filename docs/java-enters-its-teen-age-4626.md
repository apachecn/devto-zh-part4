# Java 进入青春期🎉 🎂 ⒔

> 原文：<https://dev.to/sendilkumarn/java-enters-its-teen-age-4626>

Java 13 将于 9 月 17 日发布。

随着这个版本的发布，Java 正式进入了青春期。

# JEP - 350 动态光盘档案

JVM 从磁盘加载该类。一旦加载，生成的字节码将被验证。经过验证的字节码将被 JVM 拉入内部数据结构。这些被存储在一个叫做“类数据共享(CDS)档案”的档案中。

这个初始加载将花费一些时间来启动应用程序的执行。
为了加快应用程序的启动时间，我们可以使用 CDS 存档。JVM 不是手动加载类，验证并把它们设置到内部的
数据结构中，而是从 CDS 档案中获取信息。

> 通常大多数 Java 应用程序在启动时会有数千个类需要加载。这将增加应用程序的启动时间。

到了 Java 12，我们必须手动提供一个类列表供 JVM 存档。使用`-XX:DumpLoadedClassList=list-of-classes`。

而是来自 Java 13 用`-XX:ArchiveClassesAtExit=app-cds.jsa`。JVM 将在退出时创建归档类
。存档类`app-cds.jsa`。

然后，当应用程序再次启动时，JVM 将使用这个归档文件来更快地启动。
我们必须指示 JVM 使用带有`-XXSharedArchiveFile=app-cds.jsa`的归档。

> 注意:每当类路径中有变化时，归档文件就会失效，
> JVM 必须获取归档文件的新快照。

查看更多关于动态类数据共享档案[这里](http://openjdk.java.net/jeps/350)
和更多[这里](https://openjdk.java.net/jeps/310)关于 JEP-310 类数据共享。

# JEP - 354 开关表达式(在预览模式下)

JEP-325 在 Java12 中引入了开关表达式作为预览特性。

我们可以使用下面的`case expression (or) function ->`:

```
boolean isTrue = switch (valueToCheck) {
  case "True", "Truthy", "Correct", "Valid" -> true;
  case "False", "Falsey", "InCorrect", "Invalid" -> false;
  default -> throw new NotValidException("some valid message");
} 
```

或者`case expression (or) function :` :

```
boolean isTrue = switch (valueToCheck) {
  case "True", "Truthy", "Correct", "Valid":
    yield true;
  case "False", "Falsey", "InCorrect", "Invalid":
    yield false;
  default:
    throw new NotValidException("some valid message");
} 
```

点击查看更多关于开关表达式[的信息。](http://openjdk.java.net/jeps/350)

# JEP - 355 文本块(在预览模式下)

多行字符串或文本块是 Java 中期待已久的特性。Java 应用程序中有大量的 SQL 查询、XML 或 HTML，处理多行字符串既困难又麻烦。

这个特性使我们能够轻松地编写带缩进的多行字符串，而且不那么麻烦。

为了定义一个多行字符串，我们必须用`"""`来包装这个字符串。请注意，`"""`中的字符串是按照它的表示方式缩进的。如果你想修剪任何空间，使用`stripIndent`删除任何意外的空间。

```
 String html = """
              <html>
                  <body>
                      <p>Hello, world</p>
                  </body>
              </html>
              """;

String query = """
               SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
               WHERE `CITY` = 'INDIANAPOLIS'
               ORDER BY `EMP_ID`, `LAST_NAME`;
               """; 
```

点击查看更多关于文本块[的信息。](http://openjdk.java.net/jeps/355)

# JEP - 351 ZGC:取消未使用内存的提交

Z 垃圾收集器，也称为 ZGC，是一个可扩展的低延迟垃圾收集器，旨在满足以下目标:

*   暂停时间不超过 10 毫秒
*   暂停时间不会随着堆或活动集的大小而增加
*   处理大小从几百兆字节到几兆字节的堆

运行在云上，你应该为你使用的东西付费。在 Java 应用程序中，GC 将保留比应用程序所需多的堆大小。在低资源
环境中释放未使用的内存非常重要。其他 GC(GC1 和 [Shenandoah](https://wiki.openjdk.java.net/display/shenandoah) )将在上述时间后释放
未使用的内存。

类似地，我们可以使用下面的
选项`-XX:ZUncommitDelay=<seconds>`配置 ZGC 在一段时间后释放未使用的内存。

这将增加您的应用程序可以使用的资源。

点击查看更多关于此功能的信息[。](http://openjdk.java.net/jeps/351)

同样，对于 Z 垃圾收集器，我们可以使用`-XXSoftMaxHeapSize=<bytes>`指定最大堆大小。这防止了
堆大小在指定的值之内。请记住，这是一个软限制，当
绝对有必要避免 OOM 时，JVM 可以增加堆的大小，直到`-Xmx`。

Z 垃圾收集器现在可以使用堆大小的`16TB`。(这是巨大的...).

# JEP - 353 重新实现传统套接字 API

[java.net.Socket](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/net/Socket.java) 和 [java.net.ServerSocket](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/net/ServerSocket.java) 是一段用一些原生代码(Java
和 C)编写的旧代码。

用`NioSocketImpl`代替[的 PlainSocketImpl](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/net/AbstractPlainSocketImpl.java) 。本机代码完全被新的 I/O
实现(NIO)所取代。

它还用`concurrent locks`代替了难以调试的同步方法。

> 好奇为什么检查这里的和这里的会令人兴奋

这便于调试，也便于将来与 [`fibres`](https://wiki.openjdk.java.net/display/loom/Main) 对齐。

点击查看更多关于此功能的信息[。](http://openjdk.java.net/jeps/353)

> 很明显，这个版本使 Java 对云更加友好，并使语言更加现代化。