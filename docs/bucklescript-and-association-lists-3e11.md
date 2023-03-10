# 脚本和关联列表

> 原文：<https://dev.to/johnridesabike/bucklescript-and-association-lists-3e11>

*这篇文章原本是用 ReasonML 写的。我在 2021 年 5 月更新了它，使用 ReScript。*

在我之前的文章中，我们了解了 ReScript 的 Map 和 HashMap 容器。它们功能强大，您很可能希望在任何大中型的 ReScript 项目中使用它们。但是我们也看到它们可能很复杂，尤其是在使用自定义键类型时。如果您想创建一个没有样板文件的地图呢？这就是关联列表的用途。

关联列表并不是一个新概念，但在 JavaScript 中并不常见，所以对于学习 JavaScript 的人来说，一开始可能会觉得奇怪。你可以把它想象成地图的前身。它将键映射到值，但是以一种更原始的方式。

## 关联列表基本事实

*   它们比地图简单。
*   它们需要更少的样板代码。
*   它们具有常规列表的所有特征(模式匹配、不变性等。).
*   它们比地图慢。
*   它们适用于小数据，但伸缩性不好。

## 创建关联列表

关联列表不需要设置样板代码。它们实际上只是键值对的列表。做一个就这么简单:

```
let scores = list{("John", 3), ("Mary", 5), ("Servo", 2)} 
```

Enter fullscreen mode Exit fullscreen mode

这就是你要做的。我们将约翰的得分“映射”为 3 分，玛丽的得分为 5 分，依此类推。ReScript 对于关联列表没有正式的类型定义，但是签名本质上是`list(('key, 'value))`。

真的，就是这样！没有函子，没有`cmp`函数，没有任何花哨。

## 获取一个值

我们可以使用`Belt.List.getAssoc`来查找任何键。它接受以下参数:一个列表(`list(('key, 'value))`)、一个键(`'key`)和一个等式函数(`('key, 'key) => bool`)。它返回`option('value)`。

对于复杂的键类型，您可以编写自己的等式函数([类似于我们在上一篇文章](https://dev.to/johnridesabike/bucklescript-belt-s-map-and-set-customizing-key-types-2cel)中看到的`cmp`值)。但是对于基本情况，别忘了`===`只是一个函数！我们可以这样用:

```
let johnScore = Belt.List.getAssoc(scores, "John", \"==")
/* returns Some(3) */ 
```

Enter fullscreen mode Exit fullscreen mode

(注意，必须将`===`括在括号中，以便像传递值一样传递它。)

## 其他功能

`Belt.List`附带了一些其他方便的功能:

`hasAssoc`返回一个键是否存在。

```
Belt.List.hasAssoc(scores, "Mary", \"==")
/* returns true */ 
```

Enter fullscreen mode Exit fullscreen mode

`removeAssoc`删除键及其值。

```
Belt.List.removeAssoc(scores, "Servo", \"==")
/* returns [("John", 3), ("Mary", 5)] */ 
```

Enter fullscreen mode Exit fullscreen mode

`setAssoc`设置一个键和值。

```
Belt.List.setAssoc(scores, "Joel", 10, \"==")
/* returns [("Joel", 10), ("John", 3), ("Mary", 5), ("Servo", 2)] */ 
```

Enter fullscreen mode Exit fullscreen mode

当然，您可以使用任何其他接受列表的函数。你可以用`Belt.List.sort`对它们进行排序，用`Belt.List.filter`对它们进行过滤，或者你甚至可以对它们进行模式匹配:

```
switch scores {
| list{(name, score), ...rest} =>
  doSomethingWith(name, score)
  doSomethingElseWith(rest)
| list{} => doSomethingElse()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 关联列表的缺点

乍看之下，它们似乎具有地图的所有相同功能，但没有复杂性。那么有什么区别呢？它们的简单是一把双刃剑。

地图由 AVL 树提供支持，这是一种优雅的数据结构，即使使用大量的键，也能确保高效地查找值(O(log n)时间复杂度)。相比之下，链表的时间复杂度总是为 O(n)。

换个角度来看，一个有 1000 个键的地图查找一个值只需要*三倍于*的时间。对于关联列表，有 1000 个键的列表比有 10 个键的列表花费*多 100 倍*。这些时间成本不仅限于查找关键字。几乎*列表上使用的任何*函数(`setAssoc`、`hasAssoc`等)。)将以同样的速度扩展。

如果你想添加一个没有`setAssoc`的列表，你可以像一个普通的列表那样做:

```
let newScores = list{("Mike", 7), ...scores} 
```

Enter fullscreen mode Exit fullscreen mode

这比`setAssoc`要快，但是会有后果。您没有检查`"Mike"`是否已经存在，所以有可能您会得到两个具有相同键的条目。这并不总是危险的，因为`getAssoc`将返回它找到的最近的条目(只是不要试图打乱列表)，但是它降低了性能。旧的条目并没有被物理删除，所以列表会因为陈旧的数据而变得更长，使得之后的每个函数越来越慢。

## 何时使用关联列表

关联列表很方便，但最好小剂量使用。对于小数据集，它们可能比地图更有意义。我在 Coronate 的几个地方占了他们的便宜。我有最多五个条目长的数据。在如此小的尺寸下，map 的速度优势不会超过实例化它的复杂性。他们还允许我使用类型变量作为键，而不用像映射那样编写比较函数。

如果您的数据很小，并且在最初创建后不会有太大变化，那么关联列表可能会让您的生活更轻松。如果没有，那么您可能想投资一个更成熟的容器类型，比如 map 或 hashmap。