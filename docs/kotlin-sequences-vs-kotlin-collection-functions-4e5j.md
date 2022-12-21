# 科特林序列与科特林集合函数

> 原文：<https://dev.to/lemuelogbunude/kotlin-sequences-vs-kotlin-collection-functions-4e5j>

在使用集合时，了解使用 Kotlin 集合函数和 Kotlin 序列之间的区别很有好处。

Java 有一些常见的流 API 函数，如 *Map()* ， *Filter()* ， *FlatMap()* 和 *Sorted()* 。为了在 Java 中对集合执行这些操作，我们需要调用 Stream API，而在 Kotlin 中不是这样。

下面是一个 Java 程序，用来过滤一个列表，得到偶数，将偶数乘以 4，然后打印出来。

```
//Java
var scores  = Arrays.asList(1, 2, 3, 4, 5);
scores.stream()
        .filter(integer -> integer % 2 == 0)
        .map(integer -> integer * 4)
        .forEach(System.out::println); 
```

让我们在科特林尝试这样做:

```
//Kotlin
val scores = listOf(1, 2, 3, 4, 5)
scores
    .filter { it % 2 == 0 }
    .map { it * 4 }
    .forEach { println(it) } 
```

如果我的意图是编写像 Java 流一样执行的 Kotlin 代码，那么上面的代码就不是真正正确的☹️

让我们通过添加一点文本来证明输出会有所不同:

### Java 示例

```
//Java
var scores  = Arrays.asList(1, 2, 3, 4, 5);
scores.stream()
        .filter(integer -> {
            System.out.println("Filtering " + integer);
            return integer % 2 == 0;
        })
        .map(integer -> {
            System.out.println("Mapping " + integer);
            return integer * 4;
        })
        .forEach(System.out::println); 
```

输出:

```
Filtering 1
Filtering 2
Mapping 2
8
Filtering 3
Filtering 4
Mapping 4
16
Filtering 5 
```

### 科特林样

```
//Kotlin
val scores = listOf(1, 2, 3, 4, 5)
scores
    .filter {
        println("Filtering $it")
        it % 2 == 0
    }
    .map {
        println("Mapping $it")
        it * 4
    }
    .forEach { println(it) } 
```

输出:

```
Filtering 1
Filtering 2
Filtering 3
Filtering 4
Filtering 5
Mapping 2
Mapping 4
8
16 
```

嗯🤔 ...这里好像有些不对劲

## 收藏功能

当您在 Kotlin 中调用列表上的集合函数时，每个操作都会返回一个包含所需值的新集合。

在进入下一个函数之前，管道中的每个操作都处理列表中的所有元素。

示例:

```
//Kotlin
val numbers = listOf(1, 2, 3, 4, 5)

val evenMultipliedNumbers = numbers.map {
    println("Initial Map $it")
    it + 3
}.filter {
    println("Filtering $it")
    it % 2 == 0
}.map {
    println("Mapping $it")
    it * 5
}

evenMultipliedNumbers.forEach { println(it) } 
```

当我们运行上面的代码时，我们得到了输出:

```
Initial Map 1
Initial Map 2
Initial Map 3
Initial Map 4
Initial Map 5
Filtering 4
Filtering 5
Filtering 6
Filtering 7
Filtering 8
Mapping 4
Mapping 6
Mapping 8
20
30
40 
```

它获取整个列表并再次通过 **map()** 然后 **filter()** 然后 **map()** 运行每个元素。

## 科特林序列

根据您的情况，这可能不是您想要的代码执行。科特林序列可能是你需要的。

当使用 Kotlin 序列时，值是延迟计算的。

让我们稍微修改一下上面的代码来使用 Kotlin 序列:

```
//Kotlin
val numbers = listOf(1, 2, 3, 4, 5)

val evenMultipliedNumbers = numbers.asSequence().map {
    println("Initial Map $it")
    it + 3
}.filter {
    println("Filtering $it")
    it % 2 == 0
}.map {
    println("Mapping $it")
    it * 5
}

evenMultipliedNumbers.forEach { println(it) } 
```

您所需要做的就是在调用操作之前调用 *asSequence()* 。

下面是输出:

```
Initial Map 1
Filtering 4
Mapping 4
20
Initial Map 2
Filtering 5
Initial Map 3
Filtering 6
Mapping 6
30
Initial Map 4
Filtering 7
Initial Map 5
Filtering 8
Mapping 8
40 
```

当使用序列时，每个元素在前进到下一个元素之前都要经过整个函数管道。

我们以上面列表中的第一个元素*(也就是 1)* 为例。

顺序取 **1** 然后打印出*“初始地图 1”*。在该命令之后，它将 **3** 加到 **1** 。

下一个被调用的函数是 *filter()* ，所以它执行第一个命令，打印*“过滤 4”*(此时当前值是 4，不再是 1，3+1 = 4)。

因为 **4** 是偶数(4%2==0)，所以它移动到 *map()* 函数。它打印出*“映射 4”*，然后将 **4** 乘以 5。

该值现在是 **20** ，然后 *forEach()* 调用立即打印出 evenMultipliedNumbers 收到的第一个元素。

## 返回值

每个集合函数返回一个相同类型的集合，一个简单的例子:

```
//Kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val greaterNums = numbers.filter { it > 3 }
println("Variable value $greaterNums")
println("Class Type: ${greaterNums.javaClass.name}") 
```

输出:

```
Variable value [4, 5]
Class Type: java.util.ArrayList 
```

该操作的值被赋给了变量 greaterNums，该变量的类型为 ArrayList！

让我们运行完全相同的代码，但是使用序列:

```
//Kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val greaterNums = numbers.asSequence().filter { it > 3 }
println("Variable value $greaterNums")
println("Class Type: ${greaterNums.javaClass.name}") 
```

输出:

```
Variable value kotlin.sequences.FilteringSequence@4eec7777
Class Type: kotlin.sequences.FilteringSequence 
```

它返回新的序列，这意味着在内存消耗方面有更高的效率，因为在处理序列时没有分配额外的集合。

### 是否应该在所有情况下都使用序列？

尽管 Kotlin 序列看起来很闪亮，但这可能不是所有情况下的最佳解决方案。有时候，使用集合函数可能就可以了。我会把一些伟大的文章放在下面，供进一步阅读和研究序列。

[科特林序列教程](https://winterbe.com/posts/2018/07/23/kotlin-sequence-tutorial/)

[科特林序列:图解指南](https://typealias.com/guides/kotlin-sequences-illustrated-guide/)

关于科特林序列的视频教程:[科特林序列](https://www.youtube.com/watch?v=NWhM-tBSUmY)