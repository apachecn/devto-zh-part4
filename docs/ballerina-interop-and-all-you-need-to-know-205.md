# 芭蕾舞演员 Interop 和所有你需要知道的

> 原文：<https://dev.to/lankavitharana/ballerina-interop-and-all-you-need-to-know-205>

首先，我会简单介绍一下芭蕾舞演员 T1，因为这是我第一次在 dev.to 上发表关于芭蕾舞演员的帖子

**Ballerina** 是一种全新的静态类型编程语言，专门针对集成领域而开发。我以前在[媒体](https://medium.com/@lanka.vitharana)写过一些关于芭蕾舞演员语言的帖子

让我们回到正题，经常有人问我 java 和 ballerina 之间的相互关系，以及如何在 Ballerina 中使用现有的 Java 库。我不打算在这篇文章中谈论相互关系，因为这本身是一个单独的话题。这篇文章是关于如何正确使用 interop 功能在 Ballerina 中与 java 交互。

早先我们使用了一种不同的机制叫做*本地绑定*来与 java 交互，然而自从*互操作*的引入，我们强烈推荐使用互操作来代替旧的机制，事实上，现在无论如何都很难进行旧风格的绑定(尽管我们仍然有这两种机制，并且大多数标准库仍然使用旧风格)

如果有人熟悉旧式绑定，两者之间的主要区别是我们如何保存绑定细节，其中旧式的我们将它们作为注释保存在 java 端，而在 interop 中，这些细节作为注释保存在 ballerina 端。

首先，我将解释这个概念，假设您需要在 ballerina 内部调用一个 java 方法，您必须做的是定义一个与 java 签名匹配的 ballerina 方法，并在注释中指定具有 java 方法的全限定 Java 类。

所以让我们用一个简单的例子来深入研究`howto`

在这里，我们将从芭蕾舞演员端调用下面的 java 方法作为互操作函数。

```
package org.wso2.ballerina.math;

public class AddInt {

    public static long addInt(long a, long b) {
        return a + b;
    }
} 
```

所以，首先，你必须编写上面的 java 代码，并把它编译成一个 jar。正如你所看到的，上面是一个简单的静态 java 方法，它接受两个长参数(我在这里使用了`long`类型，因为芭蕾舞演员`int`是 java 端的`long`，这使得这个例子很简单)

下一步是创建芭蕾舞演员功能定义，并提供必要的链接信息。

下面是芭蕾舞演员函数定义的例子

```
public function addInt(int a, int b) returns int = @java:Method {
    class:"org/wso2/ballerina/math/AddInt"
} external; 
```

就是这么简单，你可以把`addInit`作为芭蕾舞演员侧的一个普通功能来使用。尽管如此，正如我前面说过的，为了正确构建 ballerina 模块，您必须提供包含上述`AddInit` java 类的 jar 文件。那你可以在`Ballerina.toml`文件本身中指定。下面是怎么做的。

```
[platform]
target = "java8"

    [[platform.libraries]]
    artifactId = "ballerina-math"
    version = "@project.version@"
    path = "../native-math/target/ballerina-math-1.0.0.jar"
    groupId = "org.wso2.ballerinalang"
    modules = ["math"] 
```

您可以在 GitHub repo[ballerina-math](https://github.com/lankavitharana/ballerina-math)中找到完整的示例(检查与 ballerina 版本相关的标签)

这个概念非常简单，即使我们调用一个 java 方法，也应该总是有一个芭蕾舞演员端的定义，所以从芭蕾舞演员的角度来看，做所有的类型检查和验证是非常容易的。我们对 interop 所做的唯一其他验证是对照实际的 java 实现来验证 ballerina 定义。

当涉及到调用 java 时，就出现了如何将 ballerina 类型映射到 java 类型的问题，反之亦然。以及如何处理芭蕾舞演员无法表现的实际 java 类型。为此，我们推出了一种新型手柄*。就芭蕾舞演员而言，`Handle`是一个不透明的类型，因此您可以在`handle`类型下保存任何类型的 java 值。除此之外，我们试图用 java 映射所有可能的类型，因为用`handle` s 我们不能正确地进行类型检查。例如，芭蕾舞演员的`int`在 java 中是一个`long`值。*

 *好了，现在我们有了基础，让我们来深入一个有点复杂的例子。

那么我们如何处理 java 方法重载呢？

对于它们，我们可以使用注释(`java:Method` annotation)来提供更多的细节。因为 ballerina 不允许重载，所以您必须用不同的名称定义单独的 ballerina 函数，但是使用注释，您可以指定需要调用的实际 java 方法名称。(基本上，您可以使用注释来覆盖默认行为)。

那么我们如何调用实例方法和处理 java 构造函数呢？
Fo 构造函数，可以使用`java:Constructor`标注。正如您可能已经猜到的，您也可以在注释中覆盖参数映射。您可以将 ballerina 端创建的对象引用保存为一个`handle`，然后在调用实例方法时，您必须将该句柄作为方法调用的第一个参数进行传递。好吧，为了更清楚起见，我们举个例子

```
package org.wso2.ballerina.math;

public class Substract {
    private long initialVal;

    public Substract(long initial) {
    this.initialVal = initial;
    }

    public long substractInt(long a) {
    return initialVal - a;
    }
} 
```

下面是芭蕾舞演员侧构造图

```
public function newSubstract(int initial) returns handle = @java:Constructor {
    class:"org/wso2/ballerina/math/Substract"
} external; 
```

如你所见，我们在这里使用了`java:Constructor`注释，我们返回一个`handle`类型值，它是对象实例。这里 ballerina 方法的名字并不重要(不需要用 java 构造函数名或者其他什么来匹配)，我们用参数来区分重载的构造函数。

以下是与实例方法相匹配的芭蕾舞方法。

```
public function substract(handle receiver, int b) returns int = @java:Method{
    class:"org/wso2/ballerina/math/Substract",
    name:"substractInt"
} external; 
```

注意，我使用了`name`字段来指定实际的 java 方法名(当芭蕾舞演员的方法名与实际的 java 方法名不同时)。

那么我们如何使用这些构造函数和实例方法呢？
下面是创建 java `Substract`类的实例并调用其`substractInt`实例方法的示例代码。

```
 // Creating instance
    handle sub = newSubstract(10);

    // Invoking a instance method
    int subRes = substract(sub, 5);
    io:println(subRes); 
```

我已经接触了与 java interop 相关的大部分内容，但是还有比这更复杂的场景，比如如何处理 interop 中的错误，如何进行异步调用，如何访问字段，如何使用默认参数，等等:) **Ballerina** :)中有很多很酷的特性

然而，我将在这里结束这篇文章，因为它已经有点长了，我将尝试扩展这篇文章或创建一个新的细节。(也许我必须更新帖子标题”..你需要知道的部分”而不是当前标题:)*