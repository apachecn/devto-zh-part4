# 基本 dart 列表/数组方法。

> 原文：<https://dev.to/newtonmunene_yg/essential-dart-list-array-methods-if7>

几个月来，我一直在玩旋舞，现在我开始对飞镖的概念有了很好的了解，飞镖是驱动旋舞的语言。然而，我经常发现自己在谷歌上查找数组方法。我想我会在这篇文章中提到它们，供我自己参考，也供任何面临同样困境的人参考。这些方法来自 web/js 背景，与 Javascript 数组方法非常相似。让我们潜水吧。

## 先决条件

真的没有，但我们还是要提一提。

1.  颤动/飞镖
2.  OOP 知识

你可以使用[镖靶](https://dartpad.dartlang.org/)跟随，这是一个飞镖游戏场。也可以参考[公文](https://api.dartlang.org/stable/2.4.0/dart-core/List-class.html)。

## 入门

打开[镖靶](https://dartpad.dartlang.org/)我们就开始。

将以下代码粘贴到 dartpad 中并运行它。

```
void main() {
  List<String> fruits = ['mangoes','bananas','pears','oranges'];
  print(fruits);
} 
```

Enter fullscreen mode Exit fullscreen mode

当声明我们的`fruits`变量时，您会立即注意到类型注释。当使用像 Visual Studio 代码这样的代码编辑器时，这可以提高可读性和智能感知。它也是类型安全的，这意味着除了字符串之外，不能对任何东西赋值。上面的代码也可以写成

```
void main() {
  var fruits = ['mangoes','bananas','pears','oranges'];
  print(fruits);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想添加不同类型的项目会怎样？上面的代码会起作用。但是你也可以把`List<String>`改成`List<dynamic>`。现在你可以添加整数，字符串，布尔值，甚至其他列表。

#### 常数和最终值

你能猜到当我们将列表标记为`final`或`const`时会发生什么吗？让我们找出答案。
把你的代码改成这个

```
void main() {
  final List<String> fruits = ['mangoes','bananas','pears','oranges'];
  fruits.add('apples');
  print(fruits);
} 
```

Enter fullscreen mode Exit fullscreen mode

运行它。你会注意到苹果已经被添加到我们的列表中。标记为 final 意味着我们不能重新分配变量。然而，我们可以改变我们的列表。现在让我们试试别的。

```
void main() {
  final List<String> fruits = ['mangoes','bananas','pears','oranges'];
  fruits = [...fruits,'apples'];
  print(fruits);
} 
```

Enter fullscreen mode Exit fullscreen mode

您将立即得到一个错误。如何将我们的列表标记为不可变的？是的，你已经猜到了。使用`const`关键字。我们可以用两种方法做到这一点。

```
void main() {
  final List<String> fruits = const ['mangoes','bananas','pears','oranges'];
  //fruits.add('apples');
  print(fruits);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
void main() {
  const List<String> fruits = ['mangoes','bananas','pears','oranges'];
  //fruits.add('apples');
  print(fruits);
} 
```

Enter fullscreen mode Exit fullscreen mode

取消注释`fruits.add('apples');`并尝试运行两者。这两种实现并不完全相似。等我也有了更好的认识，我会深盖。

在我们进入正题之前，还有一件事要讲。

#### 创建列表

我们已经看到了创建列表的一种方法。这是我们从本文开始就一直在使用的。我们将介绍更多可能会派上用场的方法。

1.`List(int length)`

```
void main() {
  var list = List(5);
  print(list); //output - [null, null, null, null, null]
} 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个长度为 5 的列表，以`null`作为它的条目/值。

2.`List.filled(int length,dynamic fill,{bool growable})`

```
void main() {
  var list = List.filled(5, "hello");
  print(list); // output - [hello, hello, hello, hello, hello]
} 
```

Enter fullscreen mode Exit fullscreen mode

它接受一个长度和你希望列表在每个索引处填充的任何内容。您还可以指定接受布尔值的`growable`作为可选的命名参数。

```
void main() {
  var list = List.filled(5, "hello",growable:true);
  list.add('hello');
  print(list); // output - [hello, hello, hello, hello, hello, hello]
} 
```

Enter fullscreen mode Exit fullscreen mode

`growable`决定我们是否可以添加/删除项目。

3.`List.generate(int length, dynamic generator(int index),{bool growable})`

```
void main() {
  var list = List.generate(5,(index) =>index +2,growable:true);
  print(list); //output - [2, 3, 4, 5, 6]
} 
```

Enter fullscreen mode Exit fullscreen mode

我相信你已经弄清楚发电机是怎么回事了。它接收索引，并返回您想在该索引处填充的任何内容。

4.`List.from(Iterable list,{bool growable})`

```
void main() {
  var list = List.generate(5,(index) =>"hello $index",growable:true);
  print(list); //output - [hello 0, hello 1, hello 2, hello 3, hello 4]
  var list2 = List.from(list,growable:false);
  print(list2); //output - [hello 0, hello 1, hello 2, hello 3, hello 4]
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数从另一个列表或 iterable 中创建一个新列表。

还有一些方法，但这些是最常见的。

现在我们可以开始使用这些方法了。

## 列出方法和属性

1.`add`

这个方法我们已经见过几次了。它用于将项目添加到可增长列表中。

```
void main() {
  var fruits = ['mangoes','bananas'];
  fruits.add('apples');
  print(fruits); //output - [mangoes, bananas, apples]
} 
```

Enter fullscreen mode Exit fullscreen mode

2.`remove`

此方法从可增长列表中删除对象/项目。请确保您的值是唯一的，否则它只会删除第一个匹配项。

```
void main() {
  var fruits = ['mangoes','bananas','apples'];
  fruits.remove('mangoes');
  print(fruits); //output - [bananas, apples]
} 
```

Enter fullscreen mode Exit fullscreen mode

3.`removeAt`

此方法移除指定索引处的项目

```
void main() {
  var fruits = ['mangoes','bananas','apples'];
  fruits.removeAt(1);
  print(fruits); //output - [mangoes, apples]
} 
```

Enter fullscreen mode Exit fullscreen mode

4.`addAll`

添加指定 Iterable 的所有项。

```
void main() {
  var fruits = ['mangoes','bananas','apples'];
  var morefruits = ['peaches','plums'];
  fruits.addAll(morefruits);
  print(fruits); //output - [mangoes, bananas, apples, peaches, plums]
} 
```

Enter fullscreen mode Exit fullscreen mode

5.`asMap`

返回列表的映射表示，索引作为键，数组的项作为值。

```
void main() {
  var fruits = ['mangoes','bananas','apples'];

  print(fruits.asMap()); //output - {0: mangoes, 1: bananas, 2: apples}
} 
```

Enter fullscreen mode Exit fullscreen mode

6.`insert`和`insertAll`

这些类似于`add`和`addAll`，但是您可以指定想要添加项目的索引。

```
void main() {
  var fruits = ['mangoes','bananas','apples'];
  var morefruits = ['peaches','plums'];
  fruits.insertAll(1,morefruits);
  print(fruits); //output - [mangoes, peaches, plums, bananas, apples]
} 
```

Enter fullscreen mode Exit fullscreen mode

7.`getRange`

返回指定范围内的 Iterable 项。然后可以调用`toList`将 Iterable 转换成一个列表。

```
void main() {
  var fruits = ['mangoes','bananas','apples','peaches','plums'];

  print(fruits.getRange(1,3).toList()); //output - [bananas, apples]
} 
```

Enter fullscreen mode Exit fullscreen mode

8.`fillRange`

替换指定范围内的所有项目。

```
void main() {
  var fruits = ['mangoes','bananas','apples','peaches','plums'];
  fruits.fillRange(1,4,'pears');
  print(fruits); //output - [mangoes, pears, pears, pears, plums]
} 
```

Enter fullscreen mode Exit fullscreen mode

9.`lastIndexOf`和`lastIndexWhere`

`lastIndexOf`允许您获取您传递的最后一个项目的索引。这意味着如果你有不止一个相似的项目/对象，它将得到列表中最后一个的索引。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums'];

  print(fruits.lastIndexOf('mangoes')); //output - 2
} 
```

Enter fullscreen mode Exit fullscreen mode

`lastIndexWhere`类似于`lastIndexOf`,除了它允许你指定一个测试而不是一个对象/项目。然后，它获取通过测试的最后一个项目的索引

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums'];

  print(fruits.lastIndexWhere((item)=> item == 'mangoes')); //output - 2
} 
```

Enter fullscreen mode Exit fullscreen mode

10.`shuffle`

随机打乱列表中的项目

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums'];
  fruits.shuffle();
  print(fruits); //output - [bananas, plums, peaches, mangoes, mangoes]
} 
```

Enter fullscreen mode Exit fullscreen mode

11.`sort`

根据传递的参数对列表中的项目进行排序，该参数通常是一个[可比的](https://api.dartlang.org/be/165327/dart-core/Comparable-class.html)

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];
  fruits.sort((a,b)=> a.compareTo(b));
  print(fruits); //output - [apples, bananas, mangoes, mangoes, peaches, plums]
} 
```

Enter fullscreen mode Exit fullscreen mode

```
void main() {
  var numbers= [30,10,22,45,24,88,1,37,100,0];
  fruits.sort((a,b)=> a - b);
  print(numbers); //output - [0, 1, 10, 22, 24, 30, 37, 45, 88, 100]
} 
```

Enter fullscreen mode Exit fullscreen mode

12.`any`

检查列表中的项目，并根据列表中的任何项目是否通过测试返回 true 或 false。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];

  print(fruits.any((fruit)=>fruit.startsWith('a'))); //output - true
} 
```

Enter fullscreen mode Exit fullscreen mode

13.`every`

这与`any`类似，除了所有项目必须通过测试，而不是只有一个项目。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];

  print(fruits.every((fruit)=>fruit is String)); //output - true
} 
```

Enter fullscreen mode Exit fullscreen mode

14.`expand`

将列表/iterable 中的每一项扩展到零个或多个项。这种方法的一个很好的用途是复制列表中的项目。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];
  print(fruits.expand((fruit)=> [fruit,fruit]).toList()); //output - [mangoes, mangoes, bananas, bananas, mangoes, mangoes, peaches, peaches, plums, plums, apples, apples]
} 
```

Enter fullscreen mode Exit fullscreen mode

15.`reduce`和`fold`

这些函数使用提供的函数组合列表中的项目，并返回它们的组合值。

```
void main() {
  var numbers = [10,20,30,40,50,60,70,80];

  print(numbers.reduce((a,b)=>a+b)); //output - 360
} 
```

Enter fullscreen mode Exit fullscreen mode

`fold`取一个初始值，加到组合值上。

```
void main() {
  var numbers = [10,20,30,40,50,60,70,80];
  print(numbers.fold(100,(a,b)=> b+a)); //output - 460
} 
```

Enter fullscreen mode Exit fullscreen mode

16.`join`

将每个项转换为一个字符串，将传递的字符串连接到所有项，并将所有项连接成一个字符串。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];

  print(fruits.join(' are fruits,')); //output - mangoes are fruits,bananas are fruits,mangoes are fruits,peaches are fruits,plums are fruits,apples
} 
```

Enter fullscreen mode Exit fullscreen mode

17.`map`

返回一个新的 Iterable，其中的每一项都被修改以适应传递的函数。然后你可以调用`toList`来转换成一个列表。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];

  print(fruits.map((fruit)=> "$fruit are fruits").toList()); //output - [mangoes are fruits, bananas are fruits, mangoes are fruits, peaches are fruits, plums are fruits, apples are fruits]
} 
```

Enter fullscreen mode Exit fullscreen mode

18.`forEach`

获取每个项目，并按其索引顺序应用给定的函数。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];
  fruits.forEach((fruit){
    print(fruit);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

19.`followedBy`

将传递的列表/iterable 添加到您的列表中。

```
void main() {
  var fruits = ['mangoes','bananas','mangoes','peaches','plums','apples'];

  print(fruits.followedBy(["coconuts","guavas"]).toList()); //output - [mangoes, bananas, mangoes, peaches, plums, apples, coconuts, guavas]
} 
```

Enter fullscreen mode Exit fullscreen mode

20.`take`

接受一个整数并返回一个列表，该列表只包含指定长度范围内的前几个元素。

```
void main() {
  var fruits = ['mangoes','bananas','peaches','plums','apples'];

  print(fruits.take(3).toList()); //output - [mangoes, bananas, peaches]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

还有一些方法我没有在这里概述。它们也很重要，我建议你看一下[官方文件](https://api.dartlang.org/stable/2.4.0/dart-core/List-class.html)来更好地理解这些方法和更多。这里列出的方法是我经常查找的方法。