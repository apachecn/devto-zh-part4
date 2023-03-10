# Dart 中的 Else if 梯形语句

> 原文：<https://dev.to/jay_tillu/else-if-ladder-statement-in-dart-15dl>

Else if ladder 是 dart 中的一种条件句。就像 ***if*** 和 ***if-else*** 一样。当我们想要检查多个条件时，我们使用 else if ladder。

### 语法

* * *

```
if (boolean_expression) {
  //Statements to be executed if the condition is true
} else if (boolean_expression) {
  //Statements to be executed if the condition is true
} else if (boolean_expression) {
  //Statements to be executed if the condition is true
} else {
  //Statements to be executed if all the conditions are false
} 
```

Enter fullscreen mode Exit fullscreen mode

*   如果布尔表达式评估为 ***真*** ，那么 If 语句内的代码块将被执行。否则，编译器将检查 else-if 的条件。

*   一旦任何 else-if 的条件为 ***真*** ，那么其他所有 else-if 条件 ***都会被跳过*** 。所以每个 else-if 条件必须是唯一的。

*   如果所有 else-if 条件评估为 ***假*** ，那么 else 的代码块将被执行。

*   这里再次说明， ***else*** 是可选的。但是如果它发生了，那么它一定是在所有 else-if 块之后。

#### 样本代码

```
void main() {
  int number = 2;

  if (number == 5) {
    print("Number is 5");
  } else if (number == 1) {
    print("Number is 1");
  } else if (number == 2) {
    print("Number is 2");
  } else {
    print("None of the number is matched");
  }
}

Output
Number is 2 
```

Enter fullscreen mode Exit fullscreen mode

这就是所有的其他如果阶梯声明的家伙。如果我错过了什么，请随时与我分享。我很乐意向你学习。直到那时，继续爱，继续编码。我一定会在下一篇文章中向您介绍。

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