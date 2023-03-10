# 省道中的 For 循环

> 原文：<https://dev.to/jay_tillu/for-loop-in-dart-3mec>

*   ***for loop*** 是一种定循环的类型(也可以无限次迭代，但在实际情况下，我们是在迭代次数已知的情况下使用)。

*   循环 的 ***执行一段代码指定的次数。***

循环 的 ***一般在迭代次数已知时使用。***

### 语法

* * *

```
for (initialization; condition; step) {
  // Statements
} 
```

Enter fullscreen mode Exit fullscreen mode

### 样本代码

* * *

```
void main() {
  for (int i = 0; i < 3; i++) {
    print(i);
  }
}

Output
0
1
2 
```

Enter fullscreen mode Exit fullscreen mode

## for 循环是如何工作的

*   首先执行 ***初始化*** 步骤，并且只执行一次。这一步允许我们声明和初始化任何循环控制。

*   在下一步中，评估 ***条件*** 。如果条件为真，则执行 for 循环体。如果为假，则控制流程跳回到 for 循环之后的下一条语句。

*   在 for 循环体执行后，控制流程跳回 ***步骤*** ，在此进行递增或递减。这个语句允许我们更新任何循环控制变量。

*   然后再次评估条件。如果为真，那么整个过程一次又一次地重复，直到条件变为假。

*   一旦条件变为假，for 循环就终止，程序流程转到 for 循环后的下一条语句。

所以，伙计们，这就是循环的*。我希望你明白 for 循环是如何在 dart 中构造和工作的。在其他语言中，只是语法发生了变化，但是循环的核心概念和工作方式对于任何语言都是一样的。因此，请花一些时间与循环，并了解它是如何工作的。*

直到那时，继续爱，继续编码。我一定会在下一篇文章中介绍您。

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