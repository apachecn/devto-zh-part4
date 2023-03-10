# Dart 中的函数类型

> 原文：<https://dev.to/jay_tillu/types-of-function-in-dart-34m7>

让我们讨论函数的类型。记住功能可以用任何方式分类。但是这里我们根据**参数**和**返回类型**对它们进行了分类。

首先，记住一个函数只能返回一种类型。并且**返回类型**和**返回值**必须相同。比如，如果你的函数是字符串类型，那么它必须返回一个字符串值。它不能返回 int 或 double 类型。

有四种主要类型的用户定义函数(基于参数和返回类型)。

1.  **无参数**和**无返回类型**的函数
2.  带有**参数**和**的函数不返回类型**
3.  没有参数和**的函数返回类型**
4.  具有**参数**和**返回类型**的函数

## 没有参数也没有返回类型的函数

* * *

#### 语法

```
void function_name(){
  // Statements
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本程序

```
void SayMyName(){
  print("Jay Tillu");    
}
main(){
  SayMyName();
}

Output
Jay Tillu 
```

Enter fullscreen mode Exit fullscreen mode

## 不带参数的函数，返回类型

* * *

在这个类别中，函数没有参数，而是有一个返回类型。

#### 语法

```
return_type function_name() {
  // Statements
  return value;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本程序

```
int ShowMyAge(){
  int age = 20;
  return age;
}
main(){
  int myAge = ShowMyAge();
  print(myAge);
}

Output
20 
```

Enter fullscreen mode Exit fullscreen mode

## 带参数且无返回类型的函数

* * *

*   这里我们的函数有参数，但是它没有任何返回类型。

*   当函数中有参数时，我们必须在调用函数时指定每个参数的值。否则，它会给出一个运行时错误。所以要小心。

#### 语法

```
function_name(args1, args2, ...argsN){
  // Statements
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本程序

```
AboutMySelf(int age, int totalGf) {
  print(age);
  print(totalGf);
}

main() {
  AboutMySelf(20, 0);
}

Output
200 
```

Enter fullscreen mode Exit fullscreen mode

## 带参数和返回类型的函数

* * *

恭喜你。！现在我们的函数既有参数又有返回类型。😎

#### 语法

```
return_type function_name(args1, args2, ...argsN) {
  //statements
  return value;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 样本程序

```
int Sum(int numberOne, int numberTwo) {
  int addition = numberOne + numberTwo;
  return addition;
}

main() {
  int mySum = Sum(20, 30);
  print(mySum);
}

Output
Sum is 50 
```

Enter fullscreen mode Exit fullscreen mode

这里如果你没有指定任何返回类型，默认情况下它被认为是 void。但是，如果没有返回类型，那么指定 void 是一个很好的做法。

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