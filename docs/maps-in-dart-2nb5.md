# Dart 中的地图

> 原文：<https://dev.to/jay_tillu/maps-in-dart-2nb5>

*   就像列表一样，地图也是一种集合。在 Maps 中，数据存储在键:值对中。键和值可以是任何类型。
*   map 是一个可增长的集合，这意味着它可以在运行时收缩和增长。
*   映射也可以包含空值。
*   有两种方法来声明地图:

1.  使用地图文字
2.  使用地图构造函数

## 使用地图文字

* * *

*   就像我们使用 var 关键字声明 list 一样，我们也可以使用 var 声明 map。

*   声明之间的主要区别是，对于声明列表我们使用，但是对于声明映射我们必须使用{}(花括号)。

*   **对于声明列表使用[ ]**

*   **用于声明地图使用{}**

#### 通过变量声明 map 的语法

```
var VariableName = {
  key : value, 
  key : value,
  key : value,
  key : value,
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 通过变量声明地图的示例代码

```
void main() {
  var myMap = {
    "id": "jay",
    "password": "1234",
    "name": "Jay Tillu",
  };
  print(myMap);
}

output
{id: jay, password: 1234, name: Jay Tillu} 
```

Enter fullscreen mode Exit fullscreen mode

#### 运行时向映射添加值的语法

```
mapName[key] = value; 
```

Enter fullscreen mode Exit fullscreen mode

#### 向地图添加值的示例代码

```
void main() {
  var myMap = {
    "id": "jay",
    "password": "1234",
    "name": "Jay Tillu",
  };
  print("************ Before adding data in Map ************");
  print(myMap);

  // Adding value to Map

  myMap["country"] = "india";
  print("************ After adding data in Map ************");
  print(myMap);
}

Output
************ Before adding data in Map ************
{id: jay, password: 1234, name: Jay Tillu}
************ After adding data in Map ************
{id: jay, password: 1234, name: Jay Tillu, country: india} 
```

Enter fullscreen mode Exit fullscreen mode

## 地图构造器

* * *

*   为了声明 Map，我们也可以使用 Map()构造函数。只是你喜欢哪种方式。如果你用标准的方法来声明 map，这并没有错。

#### 通过映射构造器声明映射的语法

```
var mapName = Map();
mapName[key] = value;   // For adding value to your Map 
```

Enter fullscreen mode Exit fullscreen mode

#### 通过映射构造器声明映射的示例代码

```
void main() {
  var myMap = Map();
  print("************ Before adding data in Map ************");
  print(myMap);

//  Adding value to Map
  myMap["id"] = "jay";
  myMap["password"] = "1234";
  myMap["country"] = "India";
  print("************ After adding data in Map ************");
  print(myMap);
}

Output
************ Before adding data in Map ************
{}
************ After adding data in Map ************
{id: jay, password: 1234, country: India} 
```

Enter fullscreen mode Exit fullscreen mode

伙计们，地图到此为止。如果我错过了什么，请随时告诉我。直到那时，继续爱，继续编码。和往常一样，我将在下一篇文章中介绍您。😊

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