# Dart 中的可选参数

> 原文：<https://dev.to/jay_tillu/optional-arguments-in-dart-1e85>

*   在正常情况下，如果你声明一个函数和它的参数，那么你必须指定每个参数的值。但是如果我们希望我们的论点是可选的，我们会怎么做。

*   可选参数的意思是，如果你指定了参数的值，那就没问题，如果你没有指定它的值，那也没问题。给可选参数赋值不是强制性的。

*   好的做法是可选参数应该放在参数列表的最后。

有三种类型的可选参数。

1.  可选位置参数
2.  可选命名参数
3.  具有默认值的可选参数

## 可选位置自变量

* * *

*   为了指定可选的位置参数，我们使用[ ]方括号。

```
function_name (argument1, [argument2]) {
  // statements
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在可选的位置参数中，如果我们不传递值，那么它将被设置为空。

```
ShowMyDetails(String name, [String lastName, int age]) {
  print(name);
  print(lastName);
  print(age);
}

main() {
  ShowMyDetails("jay", "Tillu");
}

Output

jay
Tillu
null 
```

Enter fullscreen mode Exit fullscreen mode

*   正如你在这里看到的，我没有指定年龄的值。它被打印为空。

## 可选命名参数

* * *

*   在可选的命名参数中，当您将值传递给参数时，还必须指定其名称。这就是为什么它被称为可选的命名参数。

*   这里的优点是，当我们指定参数的名称和值时，我们可以按任何顺序传递参数。

*   为了指定一个可选的命名参数，我们使用{}花括号。

#### 语法

```
function_name (argument1, {argument2}) {
  // statements
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 调用函数的语法

```
function_name (argument_Name : value); 
```

Enter fullscreen mode Exit fullscreen mode

*   在可选的位置参数中，如果我们不传递值，那么它将被设置为空。

#### 程序

```
ShowMyDetails(String name, {String lastName, int age}) {
  print(name);
  print(lastName);
  print(age);
}

main() {
  ShowMyDetails("Jay", lastName: "Tillu", age: 24);
}

Output
Jay
Tillu
24 
```

Enter fullscreen mode Exit fullscreen mode

## 可选参数有默认值

* * *

*   在可选的位置参数和可选的命名参数中，如果我们不指定参数中的值，那么它将被设置为 NULL。

*   但是如果我们想为这些参数指定默认值，会发生什么呢？在这种情况下，我们可以使用带有默认值的可选参数。

*   在这里，默认值也可以通过在函数调用时指定值来覆盖。

*   简而言之，如果在函数调用时没有指定值，它将采用默认值，但是如果在函数调用时指定了值，它将覆盖新值。所以这里你是安全的。

*   为了用默认值指定可选参数，我们使用{}花括号。

#### 语法

```
function_name (argument1, {argument2 = default_value}) {
  // statements
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 调用函数的语法

```
// if you want to override new value
function_name(argumentName : value); 
```

Enter fullscreen mode Exit fullscreen mode

#### 程序

```
ShowMyDetails(String name,
 {String lastName = "Sanket", int age = 20}){
  print(name);
  print(lastName);
  print(age);
}

main() {
  ShowMyDetails("Jay", age: 24);
}

Output
Jay
Sanket
24 
```

Enter fullscreen mode Exit fullscreen mode

*   请注意，我将 Sanket 指定为 Lastname 的默认值。当函数调用时，我没有指定任何值。编译器采用它的默认值并打印出来。

*   另一方面，age 也有它的默认值，但是当函数调用 24 时我覆盖了这个值，所以编译器打印新的值作为输出。

所以，伙计们，这就是可选论点。正如我一直说的，请围绕它探索，实践它，并试图从概念上理解它。

如果我错过了什么，请随时告诉我。直到那时，继续爱，继续编码。我一定会在下一篇文章中介绍您。

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