# 改变我想法的功能

> 原文：<https://dev.to/sierisimo/the-function-that-changed-my-mind-5dn>

写代码的时候，总要有改进的地方。改进可以以不同的方式出现。最近几年，对 CI ( *持续集成*)和 CD ( *持续交付*)的关注显著增加，就是一个证明。新的语言和技术也是如此。

然而，这并不涉及改变到一种新的语言、技术或工具。其中一些改进非常简单，比如改变工作方式，比如增加测试/抽象，改变模式等等。在我参与的几个项目中，我就是这样进行的。

**免责声明**:无论你是否亲自与我交谈过，或者参加过我在 meetups 上的一次演讲，你都可能已经听过我谈论过它，甚至在 [YouTube 上的 GDG 圣多明各(西班牙语)](https://www.youtube.com/watch?v=0KoXxoPHV4o)或 kot Lin/瓜达拉哈拉和墨西哥城的任何地方。

**另一个免责声明**:这篇文章不是关于架构——尽管它是 Android 社区的热门话题——最佳实践，甚至不是关于如何组织一个 Android 应用程序。只是一系列的变化让我看到了一些 Kotlin 的概念。此外，我在大约 3 年前开始实施上述策略，因此到这篇文章发表时，谷歌的库——像`core-ktx`——可能会被更新或改进。明确一点:值得一提的是，下面几行中提到的方法并不适用于所有场景或情况；本文中提到的解决方案取决于场景或需求本身，以及选择它们的个人原因。

## 但先…些*语境*

如果你是一个 Android 开发人员或者曾经使用过 Android，你可能会理解这个标题的双关语。如果没有，那我赶紧给你解释一下。

在 Android 开发中，应用程序上发生的一切都有一个`Context`，为了让一些东西工作，你也需要处理一个`Context`。这只不过是 Android 用来提供信息和访问应用程序的一个类。

一个特殊的情况出现在需要在你的应用上启动一个新的`Activity`的时候。简单地说，这意味着启动一个全新的屏幕或一个外部应用程序。为了完成它，首先你应该基于你的`Context`创建一个`Intent`，并传递你想要启动的【T3:

```
val myIntent = Intent(context, MyNextActivity::class.java) 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以使用这个意图从另一个`Activity`开始`MyNextActivity`:`startActivity(myIntent)`

如果您的应用程序依赖于活动来显示您的应用程序的不同部分或视图，那么您可能最终会有很多这样的调用。它们都具有相同的结构，但有几个变体，例如:

```
val someIntent = Intent(context, SomeActivity::class.java)
//Do stuff with the intent
startActivity(someIntent)

//And if you don't do things with the intent and you are in an activity
startActivity(
    Intent(this, MyNextActivity::class.java)
) 
```

Enter fullscreen mode Exit fullscreen mode

## 我的第一个问题

说实话，这段代码没有任何问题。一点也不。但是我在很多应用程序中看到过这种情况，首先你把这些调用分散在不同的地方，然后你突然在不同的地方重复两三次相同的工作，用不同的变量名但相同的参数设置相同的`Intent`,然后执行相同的调用。为了解决这些问题，大多数人通常会做两件事:

1.  对每种情况都用一种方法
2.  将所有放在一个类/对象下，只传递重复情况下需要的参数

这意味着，对于情况 1:

```
/** Inside your class that will have the calls **/
fun startNext() {
    startActivity(
        Intent(this, MyNextActivity::class.java)
    )
}

fun startSome() {
    val someIntent = Intent(this, SomeActivity::class.java)
    //Do things with intent
    startActivity(someIntent)
} 
```

Enter fullscreen mode Exit fullscreen mode

对于情况 2:

```
object AppRouter {
    fun startNext(context: Context) {
        startActivity(
            Intent(context, MyNextActivity::class.java)
        )
    }

    fun startSome(context: Context) {
        val someIntent = Intent(context, SomeActivity::class.java)
        //Do things with intent
        startActivity(someIntent)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以增加几层复杂性，但这是大多数解决方案的基础:**有一个启动/开始新活动的单点**。在我的例子中，我总是以第一种情况结束。到处都有函数似乎是一个好的解决方案，直到我以 120 个函数做几乎相同的事情结束，这并不坏，但是看到`::class.java`甚至在不同的方法内部真的让我很困扰。另外，在同一个类中看到 3 或 4 个几乎相同的函数也感觉不对。

我想要一种方法来移除它，让那个`::class.java`从任何地方消失，并且有一些更明确或者至少更可读的东西，而不用到处都是 123193 包装函数。

## 仿制药

来自 Java 世界的我看到所有的函数看起来都一样，只有一个参数改变了，这让我想到:“我可以做得更好”。于是我做了:

```
fun <T> launchActivity(context: Context, clazz: Class<T>) {
    val intent = Intent(context, clazz)
    startActivity(intent)
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个问题解决了:我们有一个单一的函数来开始我们的活动，我们可以把它作为**一个全局函数**。只要有人可以访问`Context`就可以使用这个功能。但是…

1.  Bob 叔叔说参数越少，功能越好——这是真的，更容易使用
2.  `::class.java`仍然会出现在周围
3.  如果我们想隐藏`::class.java`并提供一个更“业务”相关的抽象，我们还有很多函数

```
fun startNext(context: Context) {
    launchActivity(context, MyNextActivity::class.java)
}

fun startSome(context: Context) {
    launchActivity(context, SomeActivity::class.java)
} 
```

Enter fullscreen mode Exit fullscreen mode

看到这个…嗯，最初触发我的东西是整个`::class.java`并且仍然存在，这不是一个很大的改进…

## 继承

嗯，拥有这个**全局函数**也感觉有点不对，几乎所有使用该函数的地方都会是某种`Activity`。也许我们可以通过继承使该功能可用…

```
open class BaseLaunchActivity: Activity() {
    fun <T> launchActivity(clazz: Class<T>) {
        val intent = Intent(this, clazz)
        startActivity(intent)
    }
}

//And then…
class MyActivity : BaseLaunchActivity() {
    fun onSomeClick() {
        launchActivity(SomeActivity::class.java)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

酷！我们去掉了一个参数，我们隐藏了魔法发生的地方，并让使用它的大多数部件都可以使用这个函数。

但是等等……*最*？我们希望**所有**，而不是*大多数*……这还有其他问题:

1.  继承并不总是可能的
2.  不是所有需要代码的类都是一种`Activity`
3.  当某个类可以访问`Activity`时，它将需要以一种丑陋的方式转换
4.  仍然存在——啊，真难看！

对于铸造部分，我们将有:

```
(getActivity() as BaseLaunchActivity).launchActivity(SomeActivity::class.java) 
```

Enter fullscreen mode Exit fullscreen mode

也就是**超级超级危险**——是的，我反应过度了——因为你无法确定从现在开始以及任何时候，这个角色都会成功。为什么？好吧，你的类/组件依赖于一个容器，而类本身没有一个机制来一直检查容器有哪种类型——即使它有，也不应该以那种方式设计——如果你的功能依赖于某种类型的容器……好吧，那是糟糕的设计。

## 扩展救援功能

依赖继承来获得这种简单的功能似乎会产生更多的问题。其他方法将使用一个接口:

```
interface ActivityLauncher {
    fun <T> launchActivity(context: Context, clazz: Class<T>) {
        val intent = Intent(context, clazz)
        startActivity(intent)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们回到了有两个参数…但是在接口上我们可以强制属性:

```
interface ActivityLauncher {
    val launcherContext: Context

    fun <T> launchActivity(clazz: Class<T>) {
        val intent = Intent(launcherContext, clazz)
        startActivity(intent)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在每个需要这个函数的类都需要:

1.  实现接口
2.  给`launcherContext`属性赋值

那太无聊了！！

用一个扩展函数来消除对属性的依赖怎么样？

```
fun <T> Context.launchActivity(clazz: Class<T>) {
    val intent = Intent(this, clazz)
    startActivity(intent)
} 
```

Enter fullscreen mode Exit fullscreen mode

那实际上很酷！

现在，无论周围存在什么`Context`都可以毫无问题地简单调用这个函数，我们不再需要包装函数，但是……这个`::class.java`没有移动。啊…那东西让我们的代码看起来很丑…

我们还有一个更大的问题……原来对于这个小函数，Kotlin 编译器会生成一些曾经存在于 JVM 中的*字节码*,在 java 上看起来大概是这样的:

```
public final class ContextExtensions {
    public static final void launchActivity(@NotNull Context context, @NotNull Class clazz) {
        Intent intent = new Intent(context, clazz);
        startActivity(intent);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这并不坏，但这是对 Kotlin 扩展函数的一个警告……好吧，如果只是为了让东西可读，我们没有取得太大的进展:我们只从原始代码块中删除了一行代码。有什么可以让我们在字节码中不使用这个，并且仍然增加我们代码库的可读性呢？

## 内嵌

在我们的函数中添加一个保留字会让*字节码* :
上的一切变得很酷

```
inline fun <T> Context.launchActivity(clazz: Class<T>) {
    val intent = Intent(this, clazz)
    startActivity(intent)
} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你在 **AndroidStudio** 或 **IntelliJ** 上写这篇文章，你可能会注意到一个警告，解释说*内联*这个函数并没有实际的优化。的确如此，`inline`作为一个 ***智能复制粘贴*** 工作，这意味着它将在编译时变魔术般地粘贴你的函数体，在那里用实际的参数和东西调用。

说到 params…那`Intent`呢？我们通常使用`Intent`在活动之间传递信息，对吗？用当前的方法是不可能传递任何参数的…让我们先解决这个问题，然后我们可以检查来自`inline`的警告。

## 带接收器的λ

因为我们不想增加参数列表的大小，因为参数的类型可以增加，因为我们不想只为参数使用多个包装函数，所以我们需要找到一种方法来"*按需配置*"我们的`Intent`*。*

 *带有接收器的*λ是解决这个问题的一种方法。将这个参数添加到我们的函数中，我们可以允许每个调用者在函数执行期间配置`Intent`，并且仍然保持内容的可读性:* 

```
inline fun <T> Context.launchActivity(clazz: Class<T>, confBlock: Intent.() -> Unit) {
    val intent = Intent(this, clazz)
    intent.confBlock()
    startActivity(intent)
} 
```

Enter fullscreen mode Exit fullscreen mode

加上最后一个参数，我们说我们的函数可以接受一个将在`Intent`范围内执行的 lambda，并且可以使用来自`Intent`对象的属性和方法。这个 lambda 稍后用于设置我们的`Intent`，最后我们用已经配置好的`Intent`开始活动。

这一改变允许执行类似于:

```
fun onSomeInteraction() {
    launchActivity(SomeActivity::class.java) {
        putExtra(MY_KEY, myValue)
        putExtra(SOME_KEY, someValue)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，关于`inline`的警告随着这些变化而消失，因为优化现在有意义了。在编译时，编译器将调用我们的函数，并用函数的实际体替换它，并用局部变量替换 lambda。这将稍微增加我们的字节码的大小，但是给了我们更多的可读性。

Java 上这个调用的*字节码*看起来类似于:

```
public void onSomeInteraction() {
    Intent intent = new Intent(this, SomeActivity.class);
    intent.putExtra(MY_KEY, myValue);
    intent.putExtra(SOME_KEY, someValue);
    startActivity(intent);
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，没有对外部类的引用，没有对外部函数的调用，都生活在那里。这是一件好事，因为代码在编码时保持简单，在执行时具有真正的意义。

但是……在编码的时候,`::class.java`是存在的。如果这是科特林的现状，我为什么要抱怨呢？嗯，主要原因是因为这个操作符和类不太好，它很难看，也容易让人混淆。从一个类中获得一个`Class<T>`的方法不止一种，这是比较容易的一种。使用其他工具可能会出错，而且它们很冗长，这不是我们在这里要避免的。

## 具体化为你的类型

我们目前有一个通用的-`<T>`-函数，它是*内联的*，这两个元素可以结合起来在我们的函数中使用第三个元素:`reified` :

```
inline fun <reified Ty> Context.launchActivity(clazz: Class<T>, confBlock: Intent.() -> Unit) {
    val intent = Intent(this, clazz)
    intent.confBlock()
    startActivity(intent)
} 
```

Enter fullscreen mode Exit fullscreen mode

添加这个单词将意味着对于我们函数的每一次调用，当内联时，编译器将使用实际类型而不是调用者中的泛型或超类型——我将在下一篇文章中更深入地讨论`reified`。这个小小的改变允许我们使用函数内部的泛型作为实类型，然后我们可以移除第一个参数，用实类型替换:

```
inline fun <reified T> Context.launchActivity(confBlock: Intent.() -> Unit) {
    val intent = Intent(this, T::class.java)
    intent.confBlock()
    startActivity(intent)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在对这个函数的所有调用都可以像:
一样简洁

```
launchActivity<SomeActivity> {
    putExtra(MY_KEY, myValue)
}

launchActivity<MyNextActivity> {} 
```

Enter fullscreen mode Exit fullscreen mode

在调用时，对签名的一些最终修改将使函数更加安全、更加酷:

1.  使`T`对`Activity`有约束，只允许从`Activity`扩展的类
2.  使用接收者向*λ添加默认值，以允许具有空配置的呼叫*

```
inline fun <reified T: Activity> Context.launchActivity(confBlock: Intent.() -> Unit = {}) {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的函数比以前更酷了，不会给最终的*字节码*添加太多东西，也会让我们的代码更具可读性。哇！我们走了很长一段路，对一段代码做了很多更改，使事情变得更酷，但不仅仅如此，现在与我们一起开发的其他人也可以从这一改进中受益，因为他们可以简单地调用我们的函数，而不必担心它是如何工作的。我甚至看到有人添加动画，使用这些功能/方法来保存结果和转换数据。写地道的 Kotlin 其实是让你思考和发展与众不同，更酷！

## 结论

我们可以一步一步地应用这种方法，或者直接进入我们代码库的其他函数，一个例子是使用`SharedPreferences`并让*版*使用带有接收器的 lambda“神奇地”自动应用。我们可以创造方法，用更少的步骤配置更多的东西，这将使我们的代码编写得到改善，因为我们将向 Kotlin 上已经庞大的*功能和工具集添加工具。思考什么是重复的，什么是可以改进的，这让我们——开发人员——发现新的世界，让事情变得更酷、更清晰。不要害怕改变。*

特别感谢我的朋友 [@annaelizleal](https://dev.to/annaelizleal) 、 [@jhoon](https://github.com/jhoon) 和[亚历杭德罗·特列斯](https://github.com/gambit135)，他们在语法、错别字和修正风格方面给了我很多帮助。特别感谢你的阅读。*