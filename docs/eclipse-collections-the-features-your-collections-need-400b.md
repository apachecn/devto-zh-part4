# Eclipse 集合——您的集合需要的特性🎉⚡️👍

> 原文：<https://dev.to/sendilkumarn/eclipse-collections-the-features-your-collections-need-400b>

> 不管你信不信，数据结构很重要。选择正确的将极大地提高你的程序/产品/应用程序的性能。

许多(主流)编程语言都有一个集合库。它提供了 API 和实现，让最终用户可以轻松使用。

这些实现速度很快，旨在方便最终用户。

> 提供太多选项会增加语言的学习曲线，提供太少会导致用户执行起来很麻烦。所以语言在提供内容时必须非常小心。

# [Eclipse 集合](https://github.com/eclipse/eclipse-collections)在 Java 中提供了集合的优化和高效实现。

Eclipse 集合还添加了一些额外的数据结构，这些数据结构在核心 Java 中不是本地可用的。

但是最重要的是 Eclipse-Collections 提供了优雅、实用和流畅的 API，您可以使用它们。

我喜欢 eclipse 系列的主要原因是:

*   API 很牛逼。它们是:
    *   功能的
    *   懒惰的
    *   平行的
    *   渴望(优化时)
*   提供了`immutable`和`mutable`集合
*   提供高度优化和内存高效的实现

## 设置事物

如果你正在使用一个`Maven`项目，包括如下`eclipse collection`依赖:

```
<dependency
    <groupId>org.eclipse.collections</groupId>
    <artifactId>eclipse-collections</artifactId>
    <version>10.0.0</version>
</dependency> 
```

如果您正在使用`Gradle`，那么导入它:

```
compile 'org.eclipse.collections:eclipse-collections-api:10.0.0'
compile 'org.eclipse.collections:eclipse-collections:10.0.0' 
```

## 配料

Eclipse 集合由以下数据结构组成:

1.  目录
2.  一组
3.  地图
4.  BiMap
5.  多地图
6.  包
7.  堆
8.  结对和其他

所有这些数据结构都包括以下实现:

1.  易变的
2.  不变的
3.  懒惰的
4.  平行的
5.  整齐的
6.  分类的
7.  固定的；不变的
8.  原始和其他

> 请注意，不是所有集合的所有实现。

## 代码，代码，代码...

很少有优秀的代码表可用。他们在这里是。

让我们从一个`List`开始。

为了实例化一个新的可变列表，我们可以做以下事情:

```
MutableList<Integer> firstTenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9); 
```

可以用`of`实例化列表。

```
MutableList<Integer> firstTenNumbers = Lists.mutable.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9); 
```

### 复赛

我们可以使用经典的`get(index)`方式检索元素。Eclipse 集合还提供了一个`getFirst()`和`getLast()`方法来分别检索第一个和最后一个元素。

```
firstTenNumbers.get(4);       // 4
firstTenNumbers.getFirst();  // 0
firstTenNumbers.getLast();  // 9 
```

在函数范式中，`take`|`takeWhile`|`drop`|`dropWhile`API 非常棒，因为它们有助于在不改变列表元素的情况下从列表中删除某些元素。有了 Eclipse 集合，我们可以在`Java`做到这一点。

### 弃而取之

函数`drop`和`take`各取一个非负整数`n`。`drop`返回给定索引之后的所有元素，而`take`返回索引之前的所有元素。

```
firstTenNumbers.take(3); // 0, 1, 2
firstTenNumbers.drop(3); // 3, 4, 5, 6, 7, 8, 9 
```

功能`drop`和`take`可以由以下功能指定:

```
list.take(n) + list.drop(n) == list 
```

### dropWhile 和 takeWhile

从技术上来说，它们是`drop`和`take`，但它们没有将一个数字作为参数，而是采用了一个`predicate`函数。

`takeWhile`函数将返回所有值，直到谓词返回 true。

```
firstTenNumbers.takeWhile(i -> i % 2 == 0); // 0 
```

`dropWhile`函数将返回列表中谓词返回 true 之后的所有值。

```
firstTenNumbers.dropWhile(i -> i % 2 == 0); // 1, 2, 3, 4, 5, 6, 7, 8, 9 
```

### 转换器

很多时候，我们会需要`converters`。转换器的作用是将列表从一种形式转换成另一种形式。也就是从`Mutable`到`Immutable`，反之亦然。

我们可以分别通过`toMutable`和`toImmutable`函数来实现。

```
MutableList<T> iCanChange = Lists.mutable.with(T... args);
iCanChange.toImmutable(); // From now onwards I cannot Change

ImmutableList<T> iCannotChange = Lists.immutable.with(T... args);
iCannotChange.toMutable(); // From now on I can change 
```

我们需要反转我们的列表，API 为我们提供了`toReversed`函数来实现同样的目的。

```
MutableList<T> normalList = Lists.mutable.with(T... args);
normalList.toReversed(); 
```

栈数据结构内置在库中。我们可以使用`toStack`将`list`转换成`stack`。

```
MutableList<Integer> firstTenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
firstTenNumbers.toStack().pop(4); // 9, 8, 7, 6 
```

> 还有许多其他可用的转换器，如`toSet`、`toSortedSet`、`toMap`、`toBiMap`等

### zip

函数`zip`将获取一个`pair of lists`并将它们转换成`list of pairs`。也就是:

```
[T], [U] =>  [T, U] 
```

```
MutableList<Integer> houseNumbers = Lists.mutable.with(123, 456, 789);
MutableList<String> owners = Lists.mutable.with("Ram", "Raj", "Rahul");

owners.zip(houseNumbers); // (Ram:123), (Raj:456), (Rahul:789) 
```

> 还需要注意的是，列表的长度不必相等。

函数`zip`有很多用途，比如获取标量积。

### 选择&拒绝

`Select`和`Reject`不过是`filters`。它们都将接受一个谓词。`Select`只选择那些`true`。`Reject`只选择那些正在返回的`false`。

```
MutableList<Integer> evenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
                                        .select(i ->  i % 2 == 0);
// 0, 2, 4, 6, 8 
```

```
MutableList<Integer> oddNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
                                        .reject(i ->  i % 2 == 0);
// 1, 3, 5, 7, 9 
```

> 注意你也可以做`rejectWith`和`selectWith`。

### 分区

> PartitionMutableCollection 是基于谓词将一个可变集合拆分为两个可变集合的结果。- Eclipse 集合

```
MutableList<Integer> firstTenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
System.out.println(firstTenNumbers.partition(i -> i % 2 == 0).getSelected()); // 0, 2, 4, 6, 8 
```

`partition`将接受谓词，并根据谓词拆分列表。

> 注意你也可以做`partitionWith`。

### 群

有时我们需要将元素组合在一起，我们可以使用`groupBy`来实现。`groupBy`将接受谓词函数，并根据谓词的返回值对元素进行分组。

```
MutableList<Integer> firstTenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
System.out.println(firstTenNumbers.groupBy(i -> i % 2 == 0 ? "even" : "odd")); 
// {even=[0, 2, 4, 6, 8], odd=[1, 3, 5, 7, 9]} 
```

### 收藏

`collect`或多或少类似于`map`。它需要一个谓词函数。然后对列表中的所有值应用该函数，并返回一个包含更新值的新列表。

```
MutableList<Integer> firstTenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
System.out.println(firstTenNumbers.collect(i -> i + 1));
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

> 我们甚至可以做`flatCollect`。

### 截然不同

`Distinct`顾名思义收集数组中的`distinct`值。

```
MutableList<Integer> distinctValueList = Lists.mutable.with(1, 1, 2, 3, 4).distinct();
// [1, 2, 3, 4] 
```

### 任意满足、全部满足和非满足

`any|all|noneSatisfy`使检查变得容易，而且它的评估也很慢。例如

```
MutableList<Character> gradeList = Lists.mutable.with('A', 'A', 'F', 'B');
Boolean isPass = gradeList.allSatisfy(c -> c != 'F'); // False
Boolean isFail = gradeList.anySatisfy(c -> c == 'F'); // True
Boolean isPass = gradeList.noneSatisfy(c -> c == 'F'); // False 
```

### 最大、最小和总和

顾名思义，它们获取所提供列表中值的最大值、最小值和 sumOfInt。

```
MutableList<Integer> firstTenNumbers = Lists.mutable.with(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
System.out.println(firstTenNumbers.min()); // 0
System.out.println(firstTenNumbers.max()); // 9
System.out.println(firstTenNumbers.sumOfInt(i -> i)); // 45 
```

还有很多其他可用的 API，点击这里查看完整列表。

我们将继续关于`Eclipse Collections`的更深入的教程。

如果你喜欢这篇文章，请留下赞或评论。❤️

如果您觉得文章中有错误/遗漏之处，请随时评论:)

你可以在推特上关注我。