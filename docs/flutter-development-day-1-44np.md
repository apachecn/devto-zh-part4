# 颤振发展(第 1 天)。

> 原文：<https://dev.to/bugudiramu/flutter-development-day-1-44np>

*   在这篇文章中，我们将讨论 **Dart 编程**。
*   Dart 是一种开源的通用编程语言。
*   Dart 是一种面向对象的语言，具有 C 风格的语法，可以选择性地反编译成 JavaScript。它支持各种各样的编程辅助工具，如接口、类、集合、泛型和可选类型。
*   Dart 是用于访问 Flutter SDK 中所有方法的编程语言。
*   在这篇文章中，我将解释 Dart 的基础知识。你不需要精通飞镖，基本的就足够开始了。
*   这里可以练镖[。](https://dartpad.dartlang.org/.)
*   Dart 类似于 Java script 和 Java。程序从`main()`功能开始。

```
void main(){
print("Hello Dart")
}
<!-- Both are same -→
void main() => print("Hello Dart") 
```

*   (= >)箭头功能让我们变得更简单。当你返回一个单独的方法或语句时，你不需要用花括号把它括起来，你可以像上面一样避免使用`return`语句。
*   箭头函数是一种简洁的表示函数的机制。这些函数也称为λ函数。

* * *

Dart 包括

*   变量和运算符
*   班级
*   功能
*   表达式和编程构造
*   决策和循环结构
*   评论
*   库和包

### 注释

```
// this is single line comment

/* This is a
   Multi-line comment
*/ 
```

### 数据类型

Numbers => Integer(int)，Double(double)。
字符串= >字符串。
Booleans = >布尔型(bool)。
列表= >列表。
地图= >地图。
动态= >动态(Dynamic)。

-

```
 is also a data type we can assign any data type to `var` by default it assigns `String`.

```dart
void main() {
   var name = "Flutter Dart";
   print(name);
} 
```

输出:

```
 Flutter Dart 
```

*   `const`关键字用于表示编译时常数。使用`const`关键字声明的变量是隐式的`final`。

### 运算符

```
void main(){
  int a = 10;
  int b = 10;

  print("Addition of $a and $b: ${a+b}");
  print("Subtraction of $a and $b: ${a-b}");
  print("Multiplication of $a and $b: ${a*b}");
  print("Division of $a and $b: ${a/b}");

} 
```

输出:

```
 Addition of 10 and 10: 20
Subtraction of 10 and 10: 0
Multiplication of 10 and 10: 100
Division of 10 and 10: 1 
```

*   在这里，我们可以使用`$`直接打印变量值，对于表达式，我们使用`${}`称为**字符串插值**

### 决策

*   如果
*   否则
*   否则如果
*   开关盒

```
void main() {
   var a = 52;
   if(a > 0) {
      print("$a is positive");
   }
   else if(a < 0) {
      print("$a is negative");
   } else {
      print("$a is neither positive nor negative");
   }

   // Switch case

   var name = "Ramu";
   switch(name) {
      case "Kumar": {  print("Welcome $name"); }
      break;

      case "Indhu": {   print("Welcome $name"); }
      break;

      case "Ramu": {  print("Welcome $name"); }
      break;

      default: { print("Welcome Guest"); }
      break;
   }
} 
```

输出:

```
52 is positive
Welcome Ramu. 
```

### 循环往复

```
// For Loop
 for (var i = 0;i<4;i++){
    print("Value of i is $i");
  }
  // While Loop
  var i = 0;
  while(i<4){
    print(i);
    i++;
  }
  // Do while loop
  var i1 = 0;
  do{
    print(i1);
    i1++;
  }while(i1<4); 
```

输出:

```
// For Loop

Value of i is 0
Value of i is 1
Value of i is 2
Value of i is 3

// While Loop

Value of i is 0
Value of i is 1
Value of i is 2
Value of i is 3

// Do while loop

Value of i is 0
Value of i is 1
Value of i is 2
Value of i is 3 
```

### 列表和地图

*   列表在某些编程语言中也称为数组，对于以列表形式存储大量数据起着至关重要的作用。

*   Map 对象是一个简单的键/值对。映射中的键和值可以是任何类型。地图是一个动态集合。换句话说，地图可以在运行时增长和收缩。

```
 // List

  var list = [1,2,3,4,5,6,7,8,9];
   print(list);

//Assigning values dynamically to list

  var list1 = List(2);
  list1[0] = 'Ramu';
  list1[1] = 'Kumar';
  print(list1);

//Map

  var dict = {'Username':'Ramu','Password':'1111'};
   print(dict);
  var dict1 = {};
  dict1['1'] = 'Ramu';
  dict1['20'] = 'Kumar';
  print(dict1); 
```

输出:

```
 //List

[1, 2, 3, 4, 5, 6, 7, 8, 9]
[Ramu, Kumar]

//Map

{Username: tom, Password: pass@123}
{1: Ramu, 20: Kumar} 
```

### 功能

*   函数是可读的、可维护的和可重用的代码的组成部分。函数是一组逐步执行特定任务的语句。函数将程序组织成逻辑代码块。

```
void showMyName(String name){
    print("Welcome $name");
  }
showMyName("Ramu"); 
```

输出:

```
Welcome Ramu. 
```

给定数字的阶乘:

```
void main() => print(factorial(3));

  factorial(num){
    if(num<=0){
      return 1;
    }
    else{
      return num = factorial(num-1) * num;
    }
  } 
```

输出:

```
6 
```

### OOPS(面向对象编程)

*   Dart 是一种面向对象的语言。它支持面向对象的编程特性，如类、接口等。
*   从面向对象的角度来看，类是创建对象的蓝图。类封装了对象的数据。Dart 为这个称为类的概念提供了内置支持。

```
void main() {
  Name n1 =  Name("Bugudi","Ramu");

}
class Name {
  Name(String firstName,String lastName) {
    print("Welcome $firstName  $lastName");
  }
} 
```

输出:

```
Welcome Bugudi Ramu 
```

*   在这里，我们借助关键字`class`声明了一个类，并给出了类名。
*   构造函数是类的一个特殊函数，负责初始化类的变量。
*   然后我们创建了一个构造函数`Name(String firstName,String lastName){}`，它带有两个参数 firstName 和 lastName。然后我们将消息打印到控制台。
*   在`main()`方法中，我们为`Name`类实例化了`n1`对象，并传递了所需的两个参数。

我觉得这些知识对于 Flutter 入门来说绰绰有余。从下一个教程开始，我们将深入学习核心**颤振框架**。再见了伙计们！