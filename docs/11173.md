# 了解 Java 类路径:手动构建项目

> 原文：<https://dev.to/martingaston/understanding-the-java-classpath-building-a-project-manually-3c3l>

这是了解 Java 项目系列的第二篇文章。之前，[我们看了 Java 是如何安装到 macOS 系统上的](https://dev.to/martingaston/getting-started-with-a-java-environment-on-macos-541c)，现在我们将构建一个基本的应用程序。然后，我们将在流行的构建工具 Gradle 的帮助下，继续整合相同的应用程序，最后，我们将项目整合到 IntelliJ IDE 中。

### 我们的目录结构

我们将编译一个完全活泼的 Hello World 版本，我们过分渲染的例子将利用包、库和测试，因为它更能代表我们在野外发现的项目。这个项目是为那些对 Java 有一点了解的人设计的，不打算过多讨论工具之外的语法。

我们将松散地遵循 Maven [标准目录布局](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)的惯例，这对于我们小小的应用程序来说无疑有点大材小用——但是我们也将继续使用它，因为它代表了我们在使用 Java 时通常会遇到的惯例。下面是它的样子:

```
.
├── bin
├── lib
│   ├── jfiglet-0.0.8.jar
│   └── junit-platform-console-standalone-1.4.2.jar
└── src
    ├── main
    │   └── java
    │       └── space
    │           └── gaston
    │               ├── greeter
    │               │   └── Greeter.java
    │               ├── helloWorld
    │               │   └── HelloWorld.java
    │               └── textEffects
    │                   └── Bubble.java
    └── test
        └── java
            └── space
                └── gaston
                    ├── greeter
                    │   └── GreeterTest.java
                    └── textEffects
                        └── BubbleTest.java 
```

让我们打开这里的东西:

*   `bin`将包含我们编译的`.class`文件
*   `lib`将持有我们的第三方库。在这种情况下，我们使用 JFiglet 和 JUnit 测试运行程序，稍后我们将回到这一点。
*   `src`将存放我们的`.java`源代码。在`src`中有子目录`main`和`test`，它们都有一个`java`子目录来表示语言，然后是[标准 Java 包层次结构](https://docs.oracle.com/javase/tutorial/java/package/packages.html)。最终，经过几个月的挖掘，你终于发现了我们真正的`.java`文件。

*简单提醒一下:我还没有将`lib`文件夹签入 Git，所以如果你想自己构建这个项目，你需要从 Maven 获取 [JFiglet](https://search.maven.org/artifact/com.github.lalyos/jfiglet/0.0.8/jar) 和[JUnit](https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.4.2/junit-platform-console-standalone-1.4.2.jar)T1】文件。这在一定程度上是为了揭示手动管理依赖关系是多么复杂，并帮助我们理解，在以后，其他工具能够拯救我们是多么了不起。*

### [T1。java，。班级和。冲突](#java-class-and-jar)

好的，我刚才抛出了一些文件类型。我认为被我们的 IDE 隐藏这些细节是很常见的，所以这里有一个这些类型的快速总结:

*   一个`.java`文件是一个人类可读的 Java 源代码的纯文本文件——尽管不可否认的是，当涉及到 Java 语法的某些方面时,“可读”这个名称是有争议的。基本上，`.java`文件是我们用来存放代码的文件。
*   一个`.class`文件是编译好的 Java 字节码，可以被 Java 虚拟机执行，一个公认的运行我们的 Java 程序的时髦工具。这些冰冷、机械的文件是计算机喜欢阅读的。
*   一个`.jar`文件是一个由`.class`文件(和其他必要的资源)组成的档案，为了方便起见，它们都被整齐地压缩了。

### javac 和 java

我们的两个 JDK 二进制文件负责编译和运行我们的代码:`javac`和`java`。简而言之，`javac`负责把我们的`.java`文件变成`java`可以运行的`.class`文件。

如果我们组装了一个完整的准系统`HelloWorld.java`，那么我们可以把它作为参数输入到`javac`中，然后用`java` :
运行它

```
public class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Oh no, not another Hello World example...");
  }
} 
```

无需担心多个`.java`文件、外部库或输出目录，编译和运行这些就像发送文件路径到`javac`然后运行输出:
一样简单

```
$ javac HelloWorld.java
$ java HelloWorld
Oh no, not another Hello World example... 
```

请注意,`.class`文件当前如何被编译到与它的配套源代码相同的目录中，并且我们用没有`.class`扩展名的`java`直接调用`HelloWorld`类——后者会触发一个可怕的`java.lang.ClassNotFoundException`错误，但那是另外一个故事了。原来我们用`java`来运行 Java 类，而不是 Java `.class`文件。

### 类路径

我们现在可能看到的另一个潜在错误是`java.lang.NoClassDefFoundError`，这通常是因为有一个`.class`文件是在编译时(用`javac`创建的)，但是当我们试图用`java`运行它时，它在某个地方丢失了。

警告那些体质脆弱的人，接下来的一句话足以让你感到不适。这个可怕的想法粉碎了意志最坚强的冒险家的希望和梦想。所有进来的人，放弃所有的希望...*类路径*。

让我们放大我们的主`.java`文件:

```
.
├── greeter
│   └── Greeter.java
├── helloWorld
│   └── HelloWorld.java
└── textEffects
    └── Bubble.java 
```

我们的`Greeter.java`、`HelloWorld.java`和`Bubble.java`都被存储在不同的包中(在 Java 中，这也意味着不同的目录),并且有它们自己的需求。`HelloWorld`包括一个`Greeter`和一个`Bubble`，其中`Bubble`本身是我们的`lib`文件夹中 JFiglet `.jar`的第三方`FigletFont`类的适配器。

在我们的`test`文件夹中，我们有对`Greeter`和`Bubble`的测试，包括来自 JUnit 的`lib`中的类，以及需要测试的实际的`Greeter`和`Bubble`类。

朋友们，欢迎来到依赖。

Java 虽然很聪明，但需要知道去哪里寻找所有这些需求——因此需要类路径。我们可以从[马嘴](https://docs.oracle.com/javase/tutorial/essential/environment/paths.html)那里得到独家新闻:

> 类路径的默认值是“.”，这意味着只搜索当前目录。指定 CLASSPATH 变量或`-cp`命令行开关会覆盖该值。

另外:

> 设置`CLASSPATH`可能很棘手，应小心操作。

这很迷人。本质上，我们的类路径需要包含我们的`.jar`文件到我们的包层次结构顶部的路径。它可以通过环境变量来设置，但您不应该这样做，或者使用更好的选项`-cp`标志来设置。

它还有助于解释一个常见的 Java 陷阱，即您尝试运行`java`而不首先进入目录。

```
$ java bin.space.gaston.helloworld.HelloWorld
Error: Could not find or load main class bin.space.gaston.helloworld.HelloWorld
Caused by: java.lang.NoClassDefFoundError: space/gaston/helloworld/HelloWorld (wrong name: bin/space/gaston/helloworld/HelloWorld) 
```

你看，`bin`不是 Java 包层次结构的一部分，但是如果你在文件夹之外，它需要在类路径中。所以下面两个都可以:

```
$ cd bin
$ java space.gaston.helloworld.HelloWorld 
```

```
$ java -cp bin space.gaston.helloworld.HelloWorld 
```

### 汇集一切

记住所有这些，我们可以开始将我们的文件编译到`bin`目录中。从项目目录的顶部，我们可以开始工作。

#### 1。编译我们的`main`文件夹:

```
$ javac -d bin -cp lib/jfiglet-0.0.8.jar src/main/java/space/gaston/greeter/Greeter.java src/main/java/space/gaston/helloWorld/HelloWorld.java src/main/java/space/gaston/textEffects/Bubble.java 
```

我们现在使用`-d`标志来指定编译后的文件应该在哪里结束。我们手动将每个`.java`文件输入到`javac`，所以我们不需要将它们添加到类路径中，但是我们需要添加 JFiglet `.jar`文件，这样`Bubble`就可以编译了。

#### 2。编译我们的`test`文件夹:

```
$ javac -d bin -cp lib/junit-platform-console-standalone-1.4.2.jar:lib/jfiglet-0.0.8.jar src/main/java/space/gaston/textEffects/Bubble.java src/test/java/space/gaston/textEffects/BubbleTest.java src/main/java/space/gaston/greeter/Greeter.java src/test/java/space/gaston/greeter/GreeterTest.java 
```

我们需要将 JFiglet 和 JUnit `.jar`文件添加到我们的类路径中，现在我们还需要将每个测试文件及其测试文件输入到编译器中。我们可以有效地合并第 1 步和第 2 步，但出于演示目的，最好将它们分开，因为我认为这有助于说明发生了什么。

我们的 bin 文件现在看起来像这样——注意我们的`.class`文件的目录结构必须保持与`.java`源文件相同的包层次结构:

```
.
├── BubbleTests.class
├── GreeterTests.class
└── space
    └── gaston
        ├── greeter
        │   └── Greeter.class
        ├── helloworld
        │   └── HelloWorld.class
        └── textEffects
            └── Bubble.class 
```

#### 3。运行我们的测试:

```
$ java -jar lib/junit-platform-console-standalone-1.4.2.jar -cp bin:lib/jfiglet-0.0.8.jar --scan-class-path 
```

```
╷
├─ JUnit Jupiter ✔
│  ├─ BubbleTests ✔
│  │  └─ helloReturnsAsciiHello() ✔
│  └─ GreeterTests ✔
│     ├─ greetWithArgumentSteveReturnsHelloSteve() ✔
│     └─ greetWithNoArgsReturnsHelloWorld() ✔
└─ JUnit Vintage ✔ 
```

我们的 JUnit 版本可以在命令行上运行。我们传入了`--scan-class-path`标志，以自动让 JUnit 在我们的类路径中查找所有测试，所以这需要将`bin`文件夹添加到类路径中(因为我们在项目文件夹的顶部)以及 JFiglet，这仍然是`Bubble`所需要的。

还有，耶，测试通过了。

#### 4。运行我们的主应用程序:

```
$ java -cp bin:lib/jfiglet-0.0.8.jar space.gaston.helloworld.HelloWorld 
```

```
 _   _          _   _             ____    _
 | | | |   ___  | | | |   ___     / ___|  | |_    ___  __   __   ___
 | |_| |  / _ \ | | | |  / _ \    \___ \  | __|  / _ \ \ \ / /  / _ \
 |  _  | |  __/ | | | | | (_) |    ___) | | |_  |  __/  \ V /  |  __/
 |_| |_|  \___| |_| |_|  \___/    |____/   \__|  \___|   \_/    \___| 
```

不，我也不知道为什么我们让它输出“你好，史蒂夫”。

太好了。我们已经开始工作了。但是，天哪，对于一个简单的、完全人为设计的应用程序来说，这是不是有点太多了？你能想象每次你对应用程序进行修改并需要重新编译时的情形吗？我不知道你怎么想，但如果我不得不这样工作超过一周，我就会永远看起来像是在扮演爱德华·蒙克的《呐喊》。

在下一篇文章中，骑兵队将会带领我们走出一生的建筑恐惧。

* * *

这篇文章对你有用吗？我真的很感谢任何评论和反馈，是否有任何事情可以做得更清楚或解释得更好。我也非常感谢任何更正！