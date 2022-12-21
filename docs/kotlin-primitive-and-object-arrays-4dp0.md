# Kotlin 原语和对象数组

> 原文：<https://dev.to/lankydandev/kotlin-primitive-and-object-arrays-4dp0>

我最初开始写这篇文章是因为我在玩一些反射代码，并认为我发现了一些有趣的东西。唉，事实绝对不是这样。相反，它只是 Kotlin 的一个基本特性，我还不需要使用或关注它。虽然这篇文章没有像我希望的那样，但我仍然认为这是一篇很好的小文章，让这个主题变得清晰。

在 Java 中，有[原语类型](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)和它们的[包装版本](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)的概念。由于自动装箱和取消装箱，类型可以在它们的原始版本和包装版本之间互换。换句话说，在大多数情况下，你可以用“T0”代替“T1”，或者用“T2”代替“T3”。如果你没有注意到最后一句中的大写字母，那么我想它可能看起来很混乱。这句话的措辞也很关键。更具体地说，“在大多数情况下”的说法。

当试图交换原始数组和包装(`Object`)数组时，自动装箱和取消装箱不起作用。例如，

```
public class PrimitiveArrays {

  public static void main(String args[]) {
    Long[] longArray = new Long[] {1L, 2L, 3L};
    takesInPrimitiveLongArray(longArray);
  }

  static void takesInPrimitiveLongArray(long[] array) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

这不起作用，试图编译它会产生以下错误:

```
error: incompatible types: Long[] cannot be converted to long[]
    takesInPrimitiveLongArray(longArray); 
```

Enter fullscreen mode Exit fullscreen mode

出于同样的原因，将方法切换为接受`Long[]`并传入一个`long[]`也会编译失败。这不是大多数 Java 开发人员会感兴趣的东西，但有助于为本文的实际内容奠定基础。

Kotlin 需要为您提供相当于 Java 的原始数组。但是，Kotlin 不允许使用与 Java 相同的语法来定义数组。在 Kotlin 中，初始化一个数组看起来像:

```
val array = Array<Long>(3)
// or
val array: Array<Long> = arrayOf(1,2,3) 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到`Array`使用泛型的事实应该突出它不是一个原始数组。这在 Java 和 Kotlin 中都是事实，泛型类型不能是原语。不然可以换成`Array<long>`，我们都开心。上面的代码编译成一个对象数组`Long[]`，而不是原语`long[]`。

这种情况在某种程度上是数组特有的。一个单独使用的 Kotlin `Long`可以编译成 JVM 字节码中的`Long`或`long`。编译的类型取决于字段的可空性。数组更加明确，所以它们的类型在编译时不会改变。

为了避免这一点，Kotlin 提供了一些类，当编译成 JVM 字节码时，这些类就变成了原始数组。

这些类别包括:

| 我的锅 | Java 语言(一种计算机语言，尤用于创建网站) |
| --- | --- |
| ByteArray | 字节[] |
| 沙拉雷 | char[] |
| 有点像 | 短[] |
| IntArray(内部阵列) | int[] |
| 隆加赖 | 龙[] |
| 双阵列 | double[] |
| 漂浮阵列 | 浮动[] |
| 布尔数组 | 布尔[] |

还有更多无符号类型数组的类。

这些类也可以在 Kotlin 和 Java 之间互换，无需任何额外的努力。

作为向您展示 Kotlin 中原始数组和包装/对象数组之间差异的最后一个证据，我想向您展示一些 Kotlin 代码，这些代码被转换成了它的 Java 对应物:

```
@file:JvmName("PrimitiveArrays")
package dev.lankydan

fun main(args: Array<String>) {
  // long and Long arrays
  val longArray = longArrayOf(1,2,3,4)
  val arrayOfLongs = arrayOf<Long>(1,2,3,4)
  // int and Integer arrays
  val intArray = intArrayOf(1,2,3,4)
  val arrayOfInts = arrayOf<Int>(1,2,3,4)
  // boolean and Boolean arrays
  val booleanArray = booleanArrayOf(true, false)
  val arrayOfBooleans = arrayOf<Boolean>(true, false)
  // char and Character arrays
  val charArray = charArrayOf('a','b','c')
  val arrayOfChars = arrayOf<Char>('a', 'b', 'c')
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 Intellij 的 Kotlin 字节码反编译程序，将代码片段反编译为:

```
public final class PrimitiveArrays {
   public static final void main(String[] args) {
      Intrinsics.checkParameterIsNotNull(args, "args");
      // long and Long arrays
      long[] var10000 = new long[]{1L, 2L, 3L, 4L};
      Long[] var9 = new Long[]{1L, 2L, 3L, 4L};
      // int and Integer arrays
      int[] var10 = new int[]{1, 2, 3, 4};
      Integer[] var11 = new Integer[]{1, 2, 3, 4};
      // boolean and Boolean arrays
      boolean[] var12 = new boolean[]{true, false};
      Boolean[] var13 = new Boolean[]{true, false};
      // char and Character arrays
      char[] var14 = new char[]{'a', 'b', 'c'};
      Character[] var15 = new Character[]{'a', 'b', 'c'};
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，注意 Kotlin 为你的数组提供了有用的初始化函数。对于原语和对象数组都是如此。其次，它们是如何编制的。比如`LongArray`变成了`long[]`，`Array<Long>`变成了`Long[]`。

现在，您可以看到这些阵列之间的差异。但是，我没有提到您应该使用哪一个。您应该像 Java 一样遵从基本类型。这是因为自动装箱和取消装箱会对应用程序的性能产生影响。

对于较小的工作负载，结果可能可以忽略不计。另一方面，对于性能关键型应用程序中的大型阵列，这种可能很小的变化会产生显著的影响。关于这个主题的更多信息可以在[这里](https://effective-java.com/2015/01/autoboxing-performance/)找到。

如果您需要在数组中存储空值，那么您仍然需要引用一个包装的/object 数组。在大多数情况下，我认为你应该能够利用原始数组，但总会有你不能的时候。也就是说，大多数时候我们都只是使用`List` s，所以这些都不重要😛。

您现在应该对像`LongArray`这样的原始数组和像`Array<Long>`这样的对象数组之间的区别有了更好的理解。如果没有，那么我让你失望了，我为此道歉😥。

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 来了解我的新文章。