# Kotlin 数据结构-数组和字符串

> 原文：<https://dev.to/levimoreira/kotlin-data-structures-arrays-and-strings-3729>

嘿，这是包含 Kotlin 数据结构基本介绍的系列文章的第一篇。我会尽可能地亲自动手，因此，我将使用破解代码面试中的问题作为使用这种数据结构的例子。今天我们将从两个最基本的开始:数组和字符串。

# 字符串

像 Java 一样，Kotlin 中的字符串是不可变的，所以我们在处理它们时必须非常小心，并注意在没有明确警告的情况下创建新字符串的情况。类 *String* 用于表示一个字符串实例，字符串的每个元素都可以通过索引访问作为一个 *Char* 实例被检索，因此你可以把它看作一个 Char 对象的数组:

```
 val word = "Hello World"
    val letterH = word[0] 
```

Enter fullscreen mode Exit fullscreen mode

因此，遍历字符串的每个元素就像使用 for 循环一样简单:

```
 for (c in word){
        println(c)
    } 
```

Enter fullscreen mode Exit fullscreen mode

与许多语言类似，您可以使用反斜杠对特殊字符进行转义，如“\n”。

Kotlin 中字符串的另一个有趣的特性是我们如何使用字符串模板。如果我们想在运行时向字符串中插入值，我们可以使用字符串模板。为此，我们需要在要插入字符串的变量前使用 *$* ，或者在计算表达式时使用花括号:

```
 val oneThousand = 1000

    println("1 thousand = $oneThousand")
    println("1 thousand and one = ${oneThousand + 1}") 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，如果我们将字节码反编译成 java 代码，我们会得到我们习惯的旧的+连接:

```
 public static final void main(@NotNull String[] args) {
      Intrinsics.checkParameterIsNotNull(args, "args");
      int oneThousand = 1000;
      String var2 = "1 thousand = " + oneThousand;
      System.out.println(var2);
      var2 = "1 thousand and one = " + (oneThousand + 1);
      System.out.println(var2);
   } 
```

Enter fullscreen mode Exit fullscreen mode

关于 Kotlin 字符串的最后一个想法是，它们使用了与 Java 中相同的字符串池概念。这意味着如果两个字符串相等(从结构上来说)，它们指向内存中的同一个位置。你可能会问，为什么这很重要？在 kotlin 中，我们可以使用结构等式==和引用等式===来比较两个变量。由于字符串池和整个字符串的不稳定性，如果我们用相同的字符串值初始化两个变量，它们将拥有相同的引用，下面的代码将运行到完成:

```
 val first = "first"
    val second = "first"

    assertTrue { first == second }
    assertTrue { first === second } 
```

Enter fullscreen mode Exit fullscreen mode

大多数 Kotlin 类型都有很好的扩展函数，使我们的工作更容易。以下是我最喜欢的几个:

### 关联()

该方法获取原始字符串的每个字符，并应用一个返回映射的转换。你可以用它来创建一个字符串的字符计数图:

```
 val s = "Levi Moreira de Albuquerque"

    val bin = s.associate { c ->
        Pair(c, s.count { s -> s == c })
    }

    print(bin)

    {L=1, e=5, v=1, i=2,  =3, M=1, o=1, r=3, a=1, d=1, A=1, l=1, b=1, u=3, q=2} 
```

Enter fullscreen mode Exit fullscreen mode

### 大写()和去大写()

它接受一个地区，并根据该地区将字符串的第一个字母大写。

### 码数

你想知道在一个字符串中是否有重复的值并且可以使用额外的空间吗？只需使用 toSet():

```
 val s1 = "Levi Moreira de Albuquerque"
    val s2 = "abcdefghijk"

    false
    true 
```

Enter fullscreen mode Exit fullscreen mode

# 数组

Kotlin 中的数组由 Array 类表示，该类的基本结构如下，这是数组的一般实现，但 kotlin 有针对原始类型的专用类，如 IntArray、FloatArray 和 DoubleArray。

```
class Array<T> private constructor() {
    val size: Int
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T): Unit
} 
```

Enter fullscreen mode Exit fullscreen mode

访问一个数组就像在 Java 中一样，你可以使用方括号和索引来访问一个位置。虽然这只是上面阐述的 get()和 set()方法的语法糖。

当以 JVM 平台为目标时，基元类型数组被转换成它们的对应物，例如 IntArray 由 int[]表示，FloatArray 由 float[]表示，等等。当我们反编译代码时，这一点就很清楚了:

```
 val s1 = IntArray(5) { index ->
        index * index
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们得到这个非常难看的结果:

```
 byte var2 = 5;
      int[] var3 = new int[var2];

      for(int var4 = 0; var4 < var2; ++var4) {
         int var6 = false;
         int var9 = var4 * var4;
         var3[var4] = var9;
      } 
```

Enter fullscreen mode Exit fullscreen mode

就像字符串一样，数组也有一些非常有趣的扩展函数，下面列出了我最喜欢的一些:

### 二元搜索

在数组中执行二分搜索法，请注意，为了使结果按预期工作，需要对数组进行排序。

### forEach

### 相交

返回包含第一个数组和给定集合中的数字的集合:

```
 val s1 = IntArray(10) { index ->
        index * index
    }

    val s2 = IntArray(20) { index -> index }
    print(s1.intersect(s2.toList()))
 [0, 1, 4, 9, 16] 
```

Enter fullscreen mode Exit fullscreen mode

### 接合

使用分隔符(默认值为逗号)创建包含数组元素的字符串。我们还可以给每个元素添加前缀/后缀。

```
 val s1 = IntArray(5) { index ->
        index * index
    }
    print(s1.joinToString(separator = " "))

    0 1 4 9 16 
```

Enter fullscreen mode Exit fullscreen mode

### 最大值、最小值、平均值、总和

对给定数组的元素执行给定操作(查找最大值、最小值或计算总和/平均值)。

# 举个例子

作为使用这种数据结构的一个例子，我们将看看《破解代码访谈》第一章中的第一个问题。我们需要设计一种算法来检测一个字符串是否只包含唯一的字符。如果我们不关心性能，我们就编造一个 O(n^2 时间算法，实现起来相当简单。但是我们希望有一个 O(n)算法，为此我们将使用一个位集。

一个位集只是一个位的数组，我们将用扩展的 ASCII 字母表中的字符数来初始化我们的数组(我做了一个假设，我们将只输入 ASCII 字符串)，因此我们将使用恒定的空间量。

想法很简单，数组在所有位置都用 **false** 初始化。我们将遍历输入字符串，检查每个字符，字符的整数版本将索引位集，如果给定位置为真，我们可以立即返回*假*，因为有一个副本(我们以前见过)，否则我们最终返回真，因为我们遍历了整个字符串，没有找到任何副本。我还在开头加了一个小勾，因为如果一个字符串大于字母表，它*必须*包含重复的字符。

```
fun isUnique(s: String): Boolean {
    if (s.length > 256) return false
    val bits = BitSet(256)
    for (c in s) {
        if (bits[c.toInt()]) {
            return false
        }
        bits[c.toInt()] = true
    }
    return true
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面的方法中看到的，位集和字符串都可以通过使用索引来访问。还有，我用过**。toInt()** 扩展方法，将一个字符转换成它的整数等价物。

我希望这有助于您了解 Kotlin 中的一些基本数据结构。在下一篇文章中，我们将讨论链接列表，到时见:)