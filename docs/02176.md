# Dart 中的 Do-While 循环

> 原文：<https://dev.to/jay_tillu/do-while-loop-in-dart-33ee>

*   **do-while 循环**是一种无限循环。

*   **do-while 循环**首先执行语句，然后检查条件。如果条件为真，它将继续执行，如果条件为假，循环将终止。

*   所以不管条件是真还是假，do while 循环的语句至少会执行一次。

### 语法

* * *

```
do {
  Statement(s) to be executed;  
} while (expression); 
```

Enter fullscreen mode Exit fullscreen mode

*   这里还要注意，在 **for 循环**或 **while 循环**中，我们没有在循环末尾使用分号。但是在 do while 循环中，while 语句后必须有分号。

*   递增器或递减器必须放在 do 块内，不能放在 **do 块**外。

### 程序

* * *

```
main() {
  int number = 0;
  do {
    if (number % 2 == 0) {
      print(number);
    }
      number++;
  } while (number < 10);
}

Output
0
2
4
6
8 
```

Enter fullscreen mode Exit fullscreen mode

对于 do-while 循环的人来说就是这样。如果我错过了什么，请随时和我分享，我很乐意向你学习。

记住没有老师，没有书，没有视频教程，也没有博客能教会你一切。有人说，学习是一个旅程，旅程永无止境。只是从这里那里收集一些数据，读一读，学一学，练一练，试着去应用。不要因为做不到或者不知道这个概念或者那个概念而犹豫。记住，每个程序员都是从你现在走的这条路上走过的。记住每个大师都曾经是初学者。努力工作，全力以赴。

直到那时，继续爱，继续编码。和往常一样，我一定会在下一篇文章中赶上您。

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