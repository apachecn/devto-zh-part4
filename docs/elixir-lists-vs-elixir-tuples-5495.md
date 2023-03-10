# 酏剂列表与酏剂元组

> 原文：<https://dev.to/logicmason/elixir-lists-vs-elixir-tuples-5495>

除了[图](https://alchemist.camp/articles/how-to-update-elixir-maps)之外，列表和元组都是无处不在的，这让长生不老药的学习者有些惊讶。这里有一个快速指南。

### 列表是*链表*

Elixir 列表看起来就像 C、Java 或 JavaScript 中的数组，但它们不是。它们在内存中不是连续排列的。它们是一系列节点，每个节点保存一段数据和一个指向下一个节点的指针。最后一个节点不指向任何东西。

这对性能有影响。为了读取列表中的最后一个元素，你必须从头到尾遍历列表中的每个元素！将一个元素前置到列表的前面是很便宜的，O(1)，但是访问末尾的元素是 O(n)

### 元组更像数组

酏剂元组连续存储在内存中。访问任何元素都是 O(1)。另一方面，插入或删除一个元素需要复制整个元组，这是 O(n)。记住，像语言中的其他东西一样，元组是不可变的！

### 列表元素是*通常*的同一类型

这与其说是一条规则，不如说是一种惯例。由于列表实现了`Enumerable`协议，并且使用像`Enum.map`、`Enum.reduce`和`Enum.each`这样的函数将每个元素传递给一个函数是很常见的，如果列表中的所有元素都具有相同的形状，那么生活会更容易。

另一方面，元组通常是混合类型。它们通常从函数和模式匹配返回，在这种情况下，第一个元素通常是状态。例如:

```
case retrieve_stuff do
  {:ok, stuff} -> process(stuff)
  {:err, msg} -> Logger.error("Error with message: %{msg}")
end 
```

### 转换列表和元组很容易

您可以用`List.to_tuple(some_list)`将列表转换成元组，也可以用`Tuple.to_list(some_tuple)`将元组转换成列表。比如:

```
my_list = [1, 2, 3]
my_tuple = {:a, :b, :c}

other_tuple = List.to_tuple(my_list)   # equals {1, 2, 3}
other_list = Tuple.to_list(my_tuple)   # equals [:a, :b, :c] 
```

* * *

## 常见问题

#### 如何获取一个列表的第 n 个元素？

使用`Enum.at/2`。例如，`Enum.at([1, 2, 3, 4], 2)`将返回`3`，因为列表是零索引的。

#### 如何获取一个元组的第 n 个元素？

使用`Kernel.elem/2`。例如，`elem({:a, :b, :c}, 0)`将返回`:a`，因为元组也是零索引的。

#### 如何在列表中插入元素？

使用`List.insert_at/3`。例如，`List.insert_at([:foo, :bar, :baz], 1, :yolo)`返回`[:foo, :yolo, :bar, :baz]`。

您可以使用`List.delete_at/2`以同样的方式删除元素。

如果你打算放在列表的开头(这很有性能)，那么你也可以使用一个 cons 语法并插入一个元素，像这样:

```
my_list = [1, 2, 3]
[:hello | my_list]  # returns [:hello, 1, 2, 3] 
```

#### 如何将一个元素插入到一个元组中？

使用`Tuple.insert_at/3`。例如，`Tuple.insert_at({:ok, :stuff}, 1, :yolo)`返回`{:ok, :yolo, :stuff}`。

您可以使用`Tuple.delete_at/2`以同样的方式删除元素。

#### 如何获取一个链表或元组的长度？

对元组使用`Kernel.tuple_size/1`，对列表使用`Enum.count/1`。

#### 如果我需要了解更多信息，有什么好的参考资料？

最好直接找官方文档！

*   [仙丹文件:列表](https://hexdocs.pm/elixir/List.html)
*   [仙丹文档:元组](https://hexdocs.pm/elixir/Tuple.html)

*通过电子邮件向[炼金术士索取免费的灵药课程。](https://alchemist967856.typeform.com/to/FA0SUF)营地*