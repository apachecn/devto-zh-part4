# 功能界面:科特林的自我厌恶

> 原文：<https://dev.to/ranilch/functional-interfaces-self-loathing-in-kotlin-4bce>

> 这篇文章是以前在 Medium 上的文章的副本([初始](https://medium.com/@ranilch/self-loathing-in-kotlin-d17431bcf21e)，[后续](https://medium.com/@ranilch/functional-interfaces-self-loathing-in-kotlin-follow-up-26a23868858c))，但是因为我打算删除我的 Medium 帐户，所以我把它们移到了这里。

Kotlin 是一种很棒的编程语言。在从事了大约 12 年的 Java 编程后，与 Kotlin 一起工作感觉就像戴上了多年斜视后的眼镜:有太多东西值得去爱。

但是就像每一段感情一样，有些怪癖你只会在以后的生活中发现。在将越来越多的 Java 代码移植到 Kotlin 代码之后，我注意到了一些相当奇怪的事情，坦率地说有点烦人。

这是科特林处理 T2 功能接口的方式。

# Java 7:一场来自过去的爆炸

让我们回到没有兰姆达斯的世界。太冗长了！

```
interface JavaInterface {
    String doSomething(Item item);
}

String delegateWork(JavaInterface f) {
    return f.doSomething(item);
}

void doWork() {
    delegateWork(new JavaInterface() {
        @Override
        public String doSomething(Item item) {
            return "Item = " + item;
        }
    });
} 
```

[![](img/b90123c77e85eaf74fd8e5888a9ec253.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cMvmIOYd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3af1x7zj1x62za61vwq5.jpg)

# Java 8: Lambdas 来救场！

最后，Java 8 给了我们 Lambdas，我们可以摆脱大量代码，专注于重要的事情。此外，我们不必为每个简单的函数编写自己的函数接口，我们可以使用 oracle 提供的一些函数，例如:`java.util.function.Function<T, R>`

```
@FunctionalInterface
interface JavaInterface {
    String doSomething(Item item);
}

String delegateWork(JavaInterface f) {
    return f.doSomething(item);
}

String delegateOtherWork(Function<Item, String> f) {
    return f.apply(item);
}

void doWork() {
    delegateWork(item -> "Item = " + item);
    delegateOtherWork(item -> "Item = " + item);
} 
```

一切都很好，直到你意识到即使你现在有了函数类型，它们仍然不是语言中的一等公民。想要证据吗？猜猜 Java 中需要引入多少“函数类型”?一个？三个？五个？

# 43！

不信你自己看:[https://docs . Oracle . com/javase/8/docs/API/Java/util/function/package-summary . html](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)

如果这对你来说还不够，再添加 jOOL，你就可以访问 35 个以上的:[https://github . com/jOOQ/jOOL/tree/master/jOOL/src/main/Java/org/jOOQ/lambda/function](https://github.com/jOOQ/jOOL/tree/master/jOOL/src/main/java/org/jooq/lambda/function)
因为谁不喜欢看到这样的方法签名:

```
Function5<String, String, String, String, String, Tuple3<String, String, String>> higherOrder(Function12<String, Integer, String, Object, Object, Object, BiFunction<String, Integer, String>, String, Integer, Long, String, Double, Optional<Tuple2<String, String>>>) 
```

😜旁注:jOOL 实际上是一个非常整洁的库，值得一试。

# 科特林帮帮我们！

现在让我们把科特林加入到这个组合中。在科特林函数是一等公民。所以不需要记住几十个略有不同的函数类型。你只需要记住 Kotlin 的函数类型语法:

```
(Parameter1Type, Parameter2Type, ParameterNType) -> ReturnType 
```

就这样，就这样。

# 天堂里的烦恼

好吧，我们为什么在这里，有什么问题吗？

如前所述，随着我将越来越多的代码从 Java 迁移到 Kotlin。我在使用自定义功能接口时遇到了一些问题。因为有时你想要额外的描述性。

让我们回到 Java 8 的例子

```
@FunctionalInterface
interface JavaInterface {

    String doSomething(Item item);
}

class JavaComponent {

    private Item item = new Item();

    String delegateWork(JavaInterface f) {
        return f.doSomething(item);
    }

    String delegateOtherWork(Function<Item, String> f) {
        return f.apply(item);
    }
} 
```

现在让我们使用科特林代码
中的代码

```
delegateWork { "Print $it" }
delegateOtherWork { "Print $it" } 
```

很好，这太棒了，正是我们所期望的！好了，现在让我们将这个`JavaComponent`类迁移到 Kotlin。请注意，我们已经将`java.util.function.Function<Item, String>`更改为科特林函数类型`(Item) -> String`T3】

```
class KotlinComponent(private val item: Item = Item()) {

    fun delegateWork(f: JavaInterface): String {
        return f.doSomething(item)
    }

    fun delegateOtherWork(f: (Item) -> String): String {
        return f.invoke(item)
    }
} 
```

让我们看看在 Java 代码中使用这些高阶函数时会发生什么。

```
delegateWork(item -> "Print: " + item);
delegateOtherWork(item -> "Print: " + item); 
```

我们可以对两种方法使用相同的 lambda。让我们看看当我们在 Kotlin 中做了预期的事情后会发生什么

```
delegateWork { "Print $it" }

Error: Kotlin: Type mismatch: inferred type is () -> String but JavaInterface was expected 
```

发生了什么事？看起来编译器不能理解 lambda 的签名和函数接口方法是一样的。[https://kot linlang . org/docs/reference/Java-interop . html # Sam-conversions](https://kotlinlang.org/docs/reference/java-interop.html#sam-conversions)

所以我们必须明确地说出我们的期望:

```
delegateWork(JavaInterface { "Print $it" }) 
```

我认为这很令人失望，但还不算太糟。现在让我们看看当我们也将接口迁移到 Kotlin 时会发生什么:

```
interface KotlinInterface {
    fun doSomething(item: Item): String
}

class KotlinComponent(private val item: Item = Item()) {

    fun delegateWork(f: KotlinInterface): String {
        return f.doSomething(item)
    }

    fun delegateOtherWork(f: (Item) -> String): String {
        return f.invoke(item)
    }
} 
```

当我们使用 Java 中的`KotlinComponent`类时，不出所料没有任何变化，lambdas 保持完全相同。如果我们从 Kotlin 代码中使用它会怎么样:

```
delegateWork { "Print $it" }

Error: Kotlin: Type mismatch: inferred type is () -> String but KotlinInterface was expected 
```

看来 SAM 转换又失败了。现在，如果我们像以前一样明确地提到接口，会怎么样呢？

```
delegateWork(KotlinInterface { "Print $it" })

Error: Kotlin: Interface KotlinInterface does not have constructors 
```

这也无济于事。我们需要创建一个匿名对象来使它工作:

```
delegateWork(object : KotlinInterface {
    override fun doSomething(item: Item): String {
        return "Print $item"
    }
}) 
```

呀！这感觉就像重新使用 Java 7 一样。遗憾的是，这是因为 Kotlin 还不支持 Kotlin 接口的 SAM 转换，所以我们必须创建这个匿名对象。又见:
[https://youtrack.jetbrains.com/issue/KT-7770](https://youtrack.jetbrains.com/issue/KT-7770)
【https://stackoverflow.com/a/43737962/611032】T4

# 别名时间！

那么，我们如何避免这些冗长的匿名对象，同时仍然为函数使用自定义名称呢？我们使用类型别名:

```
/**  * Very helpful comment.  */
typealias KotlinFunctionAlias = (Item) -> String

fun delegateAliasWork(f: KotlinFunctionAlias): String {
  return f.invoke(item)
} 
```

所以现在我们可以按照我们期望的方式传入一个 lambda，我们仍然可以从函数的自定义名称中获益。

```
delegateAliasWork { "Print $it" } 
```

那么一切都好了，结案了，该回家了。不幸的是不完全是。

## 迷失在翻译中

类型别名的一个小问题是，虽然可以命名函数类型，但不能命名方法名称:

```
val iface: JavaInterface = JavaInterface { "Print $it" }
iface.doSomething(item)

val alias: KotlinFunctionalAlias = { item -> "Print $item" }
alias.invoke(item)
alias(item) 
```

为类型别名和变量选择好的名称可以缓解这个问题。幸运的是，我们开发人员非常擅长命名事物😜

## 类型安全

更大的问题是，虽然类型别名给了我们一个不同的名称，但它们并不是真正不同的类型，所以我们实际上并不是类型安全的。

让我们看一个 Java 例子，它有两个具有相同方法签名的函数接口。

```
JavaInterface1 f1 = item -> "Print " + item;
JavaInterface2 f2 = item -> "Print " + item;
f1 = f2;

Error: java: incompatible types: JavaInterface2 cannot be converted to JavaInterface1 
```

这是我们所期望的，我们不想在这里混淆苹果和橘子。

如果我们对 Kotlin 类型的别名做同样的事情会发生什么？(我想你知道我要说什么)

```
var f1: KotlinFunctionAlias1 = { item -> "Print $item" }
var f2: KotlinFunctionAlias2 = { item -> "Print $item" }
var f3: (Item) -> String = { item -> "Print $item" }
f1 = f2
f2 = f3
f1 = f3 
```

这很好，编译器不会抱怨，因为就像我提到的，它们实际上不是不同的类型。他们都很简单:`(Item) -> String`

# 方案

因此，让我们快速回顾一下处理 Kotlin 接口缺少 SAM 转换的不同方法及其优缺点

## 将功能接口保留为 Java 接口

+良好的 Java 互操作性
+支持自定义方法名
+类型安全

-需要在 Kotlin lambda 前面加上接口名
-需要额外的括号
-需要维护 Java 代码

## 为 Kotlin 函数类型使用类型别名

+良好的 Java 互操作性
+易于使用

-不是类型安全的
-没有自定义方法名

## 使用内联类

我们还没有讨论的另一个选项是使用实验性的 Kotlin 内联类。您可以用一个内联类“包装”一个 Kotlin 函数。

```
inline class KotlinInlineInterface(val doSomething: (Item) -> String)

fun delegateInlineWork(f: KotlinInlineInterface): String {
    return f.doSomething.invoke(item)
}

delegateInlineWork(KotlinInlineInterface { "Print $it" }) 
```

尽管这可行，但我不认为这是使用内联类的合适方式。此外，目前还不支持 Java 互操作性:[https://kot linlang . org/docs/reference/inline-classes . html # mangling](https://kotlinlang.org/docs/reference/inline-classes.html#mangling)

## 总是使用 Kotlin 函数类型

是的，你可以在任何地方使用`(ParamT) -> ReturnT`类型。通常这就足够了，但是随着应用程序的增长，它可能会变得更难阅读和维护，并且更容易出错。

## 与匿名对象同居

当然，如果你不介意，你可以忍受匿名对象，希望有一天 Kotlin 将支持完整的 SAM 转换，并利用出色的 IDE 集成将你的匿名对象迁移到 lambdas

\ *(ツ)* /

# Jetbrains 反馈

Reddit 上有过一个简短的讨论:[https://www . Reddit . com/r/kot Lin/comments/bipj 0 q/functional _ interfaces _ self oathing _ in _ kot Lin/](https://www.reddit.com/r/Kotlin/comments/bipj0q/functional_interfaces_selfloathing_in_kotlin/)

从那以后，我收到了罗曼·伊利扎洛夫关于这个问题的回复

> ![Roman Elizarov profile image](img/652f93f22a37d9cde50cf2419ff80e20.png)【Roman elizarov】@ relizarov![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ ranich](https://twitter.com/ranilch)

我尝试了上面提到的 Kotlin 编译器选项:

```
// Gradle Kotlin DSL
tasks.withType<KotlinCompile> {
    kotlinOptions.freeCompilerArgs += "-XXLanguage:+NewInference"
}
// Gradle Groovy DSL
compileKotlin {
    kotlinOptions {
        freeCompilerArgs += "-XXLanguage:+NewInference"
    }
} 
```

如果你更喜欢其他构建系统，请参考 Kotlin 文档( [Maven](https://kotlinlang.org/docs/reference/using-maven.html) / [Ant](https://kotlinlang.org/docs/reference/using-ant.html) )来了解如何传递 Kotlin 编译器参数。

## 问题解决了？

首先让我们看看当我们在 Kotlin 代码中使用 Kotlin 函数接口时会发生什么:

```
fun delegateWork(f: KotlinInterface): String {
    return f.doSomething(item)
}

delegateWork { item -> "Print: $item" }

Error: Type mismatch: inferred type is (Nothing) -> TypeVariable(_L) but KotlinInterface was expected 
```

那么显式指定接口呢？

```
delegateWork(KotlinInterface { item -> "Print $item" }

Error: Interface KotlinInterface does not have constructors 
```

真扫兴。我们仍然需要一个匿名对象。

在 Kotlin 代码中使用一个 **Java 函数接口怎么样？** 

```
fun javaInterface(f: JavaInterface) {
    val res = f.doSomething(item)
    output(res)
}

javaInterface { item -> "Print: $item" } 
```

**终于:正如我们所料**。一切都好，啤酒当之无愧！

## 耐心的年轻绝地

如果您观察敏锐，您会在构建过程中看到这一点:

```
w: ATTENTION!
This build uses unsafe internal compiler arguments:
-XXLanguage:+NewInference

This mode is not recommended for production use,
as no stability/compatibility guarantees are given on
compiler or generated code. Use it at your own risk! 
```

这意味着什么呢？它的意思就像这里所说的:使用它还不安全。但是知道 JetBrains 正在朝那个方向努力，我建议我们现在按照以下方式做事(从最有利到最不利)

1.  将功能接口保持为 Java 代码
2.  对 Kotlin 函数类型使用类型别名(如果你可以忍受苹果和橘子的混合)
3.  与匿名对象一起生活

感谢阅读。一如既往，我乐于接受批评和反馈。