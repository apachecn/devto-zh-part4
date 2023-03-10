# Dart 界面

> 原文：<https://dev.to/jay_tillu/interface-in-dart-208j>

*   接口定义了任何类都必须遵循的语法。接口主要用于对类施加强制。

*   当任何类实现一个接口时，它必须覆盖接口的每个方法和实例变量。

*   但是，Dart 没有用于声明接口的语法。类声明本身是 dart 中的接口。

*   任何类都可以作为接口。类可以使用 **implements** 关键字来使用任何类作为接口。

*   一旦任何类被用作接口，那么就必须覆盖该类的每个方法和实例变量。简而言之，一个类必须重新定义接口的每个方法和实例变量。

#### 声明接口语法

```
class class_name implements interface_name 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本代码

```
class Person {

  void walk() {
    print("Person can walk");
  }

  void talk() {
    print("Person can talk");
  }
}
class Jay implements Person {

  @override
  void walk() {
    print("Jay can walk");
  }

  @override
  void talk() {
    print("Jay can talk");
  }
}

main() {
  Person person = new Person();
  Jay jay = new Jay();

  person.walk();
  person.talk();

  jay.walk();
  jay.talk();
}

Output
Person can walk
Person can talk
Jay can walk
Jay can talk 
```

Enter fullscreen mode Exit fullscreen mode

## 实现多个接口

* * *

*   Dart 不支持多重继承，但是 dart 可以实现多个接口。以提供相似的功率和功能。

#### 声明多个接口的语法

```
class class_name implements interface1, interface2, interface3 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本代码

```
class Person {
  String name;

  void ShowName() {
    print("My name is $name");
  }

  void walk() {
    print("Person can walk");
  }

  void talk() {
    print("Person can talk");
  }
}

class Viveki {
  String profession;

  void ShowProfession() {
    print("from class Viveki my profession is $profession");
  }
}

class Jay implements Person, Viveki {
  @override
  String profession;

  @override
  void ShowProfession() {
    print("from class Jay my Profession is $profession");
  }
@override 
  String name; 

  @override 
  void ShowName() { 
    print("From class Jay my name is $name"); 
  } 

  @override 
  void walk() { 
    print("Jay can walk"); 
  } 

  @override 
  void talk() { 
    print("Jay can talk"); 
  } 
} 

main() { 
  Person person = new Person(); 
  Jay jay = new Jay(); 
  Viveki viveki = new Viveki(); 

  person.walk(); 
  person.talk(); 
  person.name = "Person"; 
  person.ShowName(); 

  print(""); 

  jay.walk(); 
  jay.talk(); 
  jay.name = "JAY"; 
  jay.profession = "Software Development"; 
  jay.ShowProfession(); 
  jay.ShowName(); 

  print(""); 

  viveki.profession = "Chemical Engineer"; 
  viveki.ShowProfession(); 
}

Output

Person can walk
Person can talk
My name is Person

Jay can walk
Jay can talk
from class Jay my Profession is Software Development
From class Jay my name is JAY

from class Viveki my profession is Chemical Engineer 
```

Enter fullscreen mode Exit fullscreen mode

这就是界面人员的工作。如果第一招没有理解概念，请把这篇文章看两遍。对一个界面做一点研究。如果第一次尝试没有得到概念也不用担心。即使我第三次尝试也成功了。所以不用担心，练就好。

另外，如果我错过了什么，请随时告诉我。直到那时，继续爱，继续编码。我一定会在下一篇文章中介绍您。

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