# Dart 中的构造函数

> 原文：<https://dev.to/jay_tillu/constructors-in-dart-5adh>

*   构造函数是帮助创建对象的类的特殊方法(函数)。顾名思义，它构造了新的对象。

*   它通过初始化实例变量的值来帮助创建对象。所以我们可以说，它的主要目标是为实例变量提供值。

## 构造函数如何不同于类的其他方法

* * *

普通方法和构造函数之间有一些主要的区别。

1.  构造函数与类同名。
2.  构造函数没有返回类型。
3.  创建对象时会自动调用构造函数。
4.  如果我们不指定构造函数，默认的无参数构造函数将被创建。

#### 声明构造函数的语法

```
class_name (arguments) {  //If there is a block of code.
  // Constructor body
}

or

class_name (arguments); // If there is no block of code. 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本代码

```
class Student{
  Student(int enNum){
    print(enNum);
  }
}

main(){
  var myStudent = new Student(15);
}

Output
15 
```

Enter fullscreen mode Exit fullscreen mode

## 命名构造函数

* * *

*   看到构造函数的优点后，您可能想要创建多个构造函数。但是我们知道构造函数持有类的名字。那么，在这种情况下，你可能会问，我们如何创建多个构造函数并区别对待它们。然后图中来了一个**命名的构造器**。

*   通过使用命名构造函数，你可以在同一个类中创建多个构造函数。每个构造函数都有一个唯一的名字。这样你就可以识别他们了。

#### 定义命名构造函数的语法

```
class_name.constructor_name (arguments){ 
   // If there is block of code use this syntax
   // Statements
}

or

class_name.constructor_name (arguments); 
   // If there is not block of code use this syntax 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本代码

```
class Employee {
  int empID;
  String empName;
  String empDept;

  Employee.ID(this.empID); // Named Constructor Creation

  Employee.name(this.empName);

  Employee.department(this.empDept);
}

main() {
  var myEmployee01 = new Employee.ID(15);
  var myEmployee02 = new Employee.department("Testing");
  var myEmployee03 = new Employee.name("Ashu");

  print(myEmployee01.empID);
  print(myEmployee02.empDept);
  print(myEmployee03.empName);
}

Output
15
Testing
Ashu 
```

Enter fullscreen mode Exit fullscreen mode

所以，伙计们，这就是 dart 中的构造函数。同样，这也是面向对象编程的核心概念。所以稍微练习一下。如果我遗漏了什么，请随时与我分享。直到那时，继续爱，继续编码。我一定会在下一篇文章中介绍您。

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