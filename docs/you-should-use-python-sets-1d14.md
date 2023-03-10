# 你应该使用 Python 集合

> 原文：<https://dev.to/dbanty/you-should-use-python-sets-1d14>

> 这篇文章引用了大 O 符号 <sup id="fnref1">[1](#fn1)</sup> ，如果你不熟悉它(或者不记得它是如何工作的)，现在是复习的好时机。

标准 Python 库中有大量可用的数据结构，其中大多数都没有得到充分利用。今天我想特别关注一种类型，你可能会用得更多:那就是`set`。你可能已经知道计算机科学中集合的一般概念 <sup id="fnref2">[2](#fn2)</sup> ，Python 集合 <sup id="fnref3">[3](#fn3)</sup> 正是如此。

## 什么事？

对于那些还不知道的人来说，`set`是一个无序的可散列对象集合，其中不能包含任何副本。哈希部分在这里是关键，很像`dict` <sup id="fnref4">[4](#fn4)</sup> (也称为关联数组 <sup id="fnref5">[5](#fn5)</sup> )中的键，每个条目都被哈希以创建底层数据结构的索引。你可以把一个`set`想象成一个没有任何价值观的`dict`。

## 什么时候用？

在几种情况下，`set`是更受欢迎的`list`的一个非常有用的替代方案:

1.  寻找成员(例如`in`操作者)。一个`set`的查找时间是 O(1)，而一个列表的查找时间是 O(n)<sup id="fnref6">T3】6T5。</sup>
2.  如果你将**移除**成员，关于复杂性，与这里的`in`相同。
3.  如果您需要比较、对比或组合集合——`set`有所有这些操作符。
4.  如果集合中不能有任何重复值。

## 什么时候不用？

1.  如果需要集合中有多个相同值的副本。
2.  如果对象的顺序很重要
3.  如果你收藏的物品不可收藏 <sup id="fnref7">[7](#fn7)</sup>

## 怎么用？

```
set_with_values = {'a', 'b', 'c'}
other_set = set()  # Note {} would be a dict 
assert 'a' in set_with_values  # Super speedy operation 
other_set.add('c')

assert other_set < set_with_values  # This means other_set is a subset of set_with_values 
assert set_with_values > other_set  # Super set 
other_set.add('d')

# Set defines a bunch of useful operators assert set_with_values | other_set == {'a', 'b', 'c', 'd'}
assert set_with_values & other_set == {'c'}
assert set_with_values - other_set == {'a', 'b'}
assert set_with_values ^ other_set == {'a', 'b', 'd'}

set_with_values |= other_set  # You can perform updates with all of those operators assert set_with_values == {'a', 'b', 'c', 'd'}  # Note only 1 copy of 'c' 
set_with_values.remove('d')  # Again, super fast compared to lists 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

如果你还没有在你的代码中使用集合，你可能应该这样做。基本上任何时候你使用列表的`in`或者列表的`del`，你都应该考虑使用不同的结构。

* * *

1.  [https://en.wikipedia.org/wiki/Big_O_notation](https://en.wikipedia.org/wiki/Big_O_notation)↩

2.  [https://en . Wikipedia . org/wiki/set _(abstract _ data _ type)](https://en.wikipedia.org/wiki/Set_(abstract_data_type))↩

3.  [https://docs . python . org/3.7/library/STD types . html # set-types-set-frozenset](https://docs.python.org/3.7/library/stdtypes.html#set-types-set-frozenset)↩

4.  [https://docs . python . org/3.7/library/STD types . html # mapping-types-dict](https://docs.python.org/3.7/library/stdtypes.html#mapping-types-dict)↩

5.  [https://en.wikipedia.org/wiki/Associative_array](https://en.wikipedia.org/wiki/Associative_array)↩

6.  [https://www . ics . UCI . edu/~ pattis/ics-33/lectures/complexity python . txt](https://www.ics.uci.edu/%7Epattis/ICS-33/lectures/complexitypython.txt)↩

7.  [https://docs.python.org/3/glossary.html#term-hashable](https://docs.python.org/3/glossary.html#term-hashable)↩