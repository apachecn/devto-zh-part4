# Dart 中的列表

> 原文：<https://dev.to/jay_tillu/list-in-dart-3eg1>

*   考虑这样一种情况，我们需要存储五个字符串值。如果我们使用编程的简单变量和数据类型概念，那么我们需要五个字符串数据类型的变量。

*   这看起来很简单，因为我们只需要存储五个字符串值。现在让我们假设我们必须存储 10000 个字符串值。现在创建 10000 个变量太费时间了。

*   为了克服这种情况，我们有一个叫做集合的概念。

*   集合用于存储数据。

在 dart 中，集合可以分为四个基本类别:

1.  目录
2.  地图
3.  一组
4.  长队

但是使用最多的是**列表**和**地图**。集合和队列用于一些特殊情况。

## 列表

* * *

*   该列表是具有相同数据类型的数据的集合。在其他语言中**列表**被称为**数组**。

*   在 list 中，数据以有序的方式存储。

*   列表中的每个元素都可以通过其索引号来访问。索引号总是从 0 开始。

有两种类型的列表:

1.  固定长度列表
2.  可增长列表

### 固定长度列表

*   在固定长度列表中，一旦列表的长度被定义，就不能在运行时改变。

#### 语法

```
List <data type> list_name = List (length of list); 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本代码

```
main() {
  List<int> marks = List(5);
  marks[0] = 25;    // Inserting values to list
  marks[1] = 35;
  marks[2] = 65;
  marks[3] = 75;
  marks[4] = 63;

  for (int elements in marks) {
    print(elements);
  }
}

Output
25
35
65
75
63 
```

Enter fullscreen mode Exit fullscreen mode

### 可种植列表

*   可增长列表的长度是动态的。它可以在运行时更改。

#### 语法

```
List <data type> list_name = List (); // Way 1
List <data type> list_name = [element1, element2]; // Way 2 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本代码

```
main() {

  List<String> countries = ["India", "Nepal"];
  for (String elements in countries) {
    print(elements);
  }
}

Output
India
Nepal 
```

Enter fullscreen mode Exit fullscreen mode

伙计们，这就是名单。就像我常说的实践它，从概念上理解它。直到那时，继续爱，继续编码。和往常一样，我一定会在下一篇文章中赶上您。😊

记住没有老师，没有书，没有视频教程，也没有博客能教会你一切。有人说，学习是一个旅程，旅程永无止境。只是从这里那里收集一些数据，读一读，学一学，练一练，试着去应用。不要因为做不到或者不知道这个概念或者那个概念而犹豫。记住，每个程序员都是从你现在走的这条路上走过的。记住每个大师都曾经是初学者。努力工作，全力以赴。

### 欲了解更多信息，请访问以下链接

*   [Fuchsia OS 官方网站](https://fuchsia.dev/)
*   [Dart 官方网站](https://dart.dev/)
*   [颤振官方网站](https://flutter.dev/)

> 想和我联系吗？以下是链接。我很乐意成为你的朋友。😊
> [Twitter](https://twitter.com/jay_tillu)
> [脸书](https://www.facebook.com/jaytillu.1314/)
> [insta gram](https://www.instagram.com/jay.tillu/)
> [Medium](https://medium.com/jay-tillu)
> 或者直接在[jayviveki13@gmail.com](mailto:jayviveki13@gmail.com)给我发邮件