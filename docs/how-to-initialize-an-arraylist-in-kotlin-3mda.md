# 如何在 Kotlin 中初始化数组列表

> 原文：<https://dev.to/renegadecoder94/how-to-initialize-an-arraylist-in-kotlin-3mda>

对于那些熟悉[如何使用 Python](https://therenegadecoder.com/series/how-to-python/) 系列的人来说，我认为尝试一下我最近使用的其他语言会很有趣。今天，让我们学习如何在 Kotlin 中初始化一个数组列表。

## 问题介绍

作为一个 Java 出身的人，我经常发现自己在使用 ArrayList 类存储数据。不幸的是，在 Java 中没有干净的初始化数组列表的方法，所以我想知道 Kotlin 是否在这个问题上有所改进。供参考，下面是我不想做的:

```
ArrayList<Integer> list = new ArrayList<Integer>()
list.add(7)
list.add(-4)
list.add(3) 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能想象的那样，这种解决方案扩展性不好。事实上，我甚至不认为它读起来很好。冗余信息太多了。我更希望能够做如下事情:

```
ArrayList<Integer> list = new ArrayList<Integer>(7, -4, 3) 
```

Enter fullscreen mode Exit fullscreen mode

而且，对于较大的数据集，最好能够将这些值分布在多行上:

```
ArrayList<Integer> list = new ArrayList<Integer>( 
    7, -4, 3, 2, 1, 3, 6, 5, 9, 11, 
    10, 7, -5, -6, 13, 6, -11, 13, 2, 1
) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，事实并非如此。有一些令人讨厌的变通办法，但是我希望 Kotlin 能在 Java 约定上有所改进。

## 方案

幸运的是，Kotlin 在 Java 的冗长性方面有了很大的改进，所以我保证有更好的方法来创建数组列表。只是方式的问题。

### 暴力初始化数组列表

自然，我们几乎可以直接翻译 Java 解决方案:

```
val list = ArrayList<Int>()
list.add(7)
list.add(-4)
list.add(3) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们创建了一个空的整数数组列表。然后，我们使用`add()`方法一次填充一个条目。

当然，我们喜欢比这更好的东西！让我们看看科特林能提供什么。

### 通过转换初始化数组列表

减少上述代码的一个方法是在将数组转换成 ArrayList 之前创建一个数组:

```
val list = intArrayOf(7, -4, 3).toCollection(ArrayList()) 
```

Enter fullscreen mode Exit fullscreen mode

在一行代码中，我们能够使用理想的语法创建一个整数数组。从该数组中，我们可以使用`toCollection()`方法并传递一个空数组列表来获得一个数组列表。然后，`toCollection()`方法用数组中的所有值填充 ArrayList。

显然，这并不理想，因为我们必须在这两种类型之间转换。如果能够直接创建和初始化数组列表，那就更好了。幸运的是，我们可以！

### 用 arrayListOf 初始化 ArrayList

事实证明，collections 库包含了一个在 Kotlin 中直接构建数组列表的函数:

```
val list = arrayListOf<Int>(7, -4, 3) 
```

Enter fullscreen mode Exit fullscreen mode

我不完全确定这种方法在幕后是如何工作的，但我想象它的工作方式类似于我们的暴力解决方案:

```
fun <T> arrayListOf(vararg elements: T): ArrayList<T> { 
    val list = ArrayList<T>() 
    for (element in elements) { 
        list.add(element) 
    } 
    return list
} 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，我们有一个简洁的方法来建立一个数组列表。太刺激了！

## 表现

由于我养成了用 Python 度量性能的习惯，我想我可以把这个想法移植到 Kotlin 上。幸运的是，有一个标准函数可以做到这一点:`measureNanoTime`。我们将用它来测试上面的每个代码片段:

```
import kotlin.system.measureNanoTime

val bruteForceTime = measureNanoTime { 
    val list = ArrayList<Int>() 
    list.add(7) 
    list.add(-4) 
    list.add(3)
}

val conversionTime = measureNanoTime { 
    val list = intArrayOf(7, -4, 3).toCollection(ArrayList())
}

val arrayListOfTime = measureNanoTime { 
    val list = arrayListOf<Int>(7, -4, 3)
}

println("Brute Force: $bruteForceTime")
println("Conversion: $conversionTime")
println("ArrayListOf: $arrayListOfTime") 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，强力方法明显比其他两种方法快，但是我不确定这些函数如何适应更多的输入。请随时尝试，并让我知道。无论如何，以下是结果:

> 蛮力:38700
> 
> 转换:14728800
> 
> 数组列表:6319000

对于这个测试，我最终使用了`measureNanoTime`而不是`measureTimeMillis`,因为对于暴力方法，我总是得到零值。这就说得通了。

作为参考，我使用 Android Studio 测试了 Windows 10 上的一切，设置了一个名为`scratch.kts`的暂存文件。还有，我用的是 Kotlin 1.3.31。

## 稍微重述一下

一如既往，这里是所有的解决方案在一个方便的地方:

```
// Brute force
val list = ArrayList<Int>()
list.add(7)
list.add(-4)
list.add(3)

// Array conversion
val list = intArrayOf(7, -4, 3).toCollection(ArrayList())

// Direct method
val list = arrayListOf<Int>(7, -4, 3) 
```

Enter fullscreen mode Exit fullscreen mode

由于我刚刚开始玩 Kotlin，这可能看起来像一个非常琐碎的教程。也就是说，我保证我会开始挖掘更有趣的话题。例如，我已经很兴奋开始镜像一些 Python 文章了。

既然你在这里，为什么不通过成为会员来支持叛逆的程序员呢？您将获得各种有趣的文章，如:

*   [与富有挑战性的大学生一起工作](https://therenegadecoder.com/blog/working-with-challenging-college-students/)
*   [我放弃工程职业的 11 个原因](https://therenegadecoder.com/blog/11-reasons-why-i-quit-my-engineering-career/)

当然，[欢迎您直接阅读新闻简报](http://eepurl.com/dB-ABr)，稍后再做决定。不管怎样，我很感激你的支持。

帖子[如何在 Kotlin](https://therenegadecoder.com/code/how-to-initialize-an-arraylist-in-kotlin/) 中初始化数组列表最先出现在[这个叛逆的程序员](https://therenegadecoder.com)上。