# c 指针 101:第 2 部分，传递指向函数的指针

> 原文：<https://dev.to/erikwhiting88/c-pointers-101-part-2-passing-pointers-to-functions-4m59>

在第 1 部分中，我们学习了指针、地址背后的基本思想，以及它们的含义。在第 2 部分，我们将讨论指针的一个主要用途，通过引用传递。这一部分会更容易理解，并且可能会使第 1 部分中的一些概念更加清晰。

# 什么是按引用传递？

在 C 语言中，当你传递一个变量给一个函数时，你实际上是传递了这个变量的一个副本给这个函数。对该变量进行的任何操作实际上都不适用于实变量。

相反，当我们通过引用传递时，我们将变量的*地址*传递给一个函数，实际上是给函数一个实变量。

## 拥抱我的类比...

我有一张纸，上面列有我所有朋友最喜欢的颜色。我有一个新朋友，莉娜，我想知道她最喜欢的颜色，并记录下来。

我给 Lina 发了一份我论文的复印件，让她加上自己的名字和喜欢的颜色，然后传真给我。现在，我可以用她最喜欢的颜色更新我的原始文件，或者用她传真给我的副本作为新的规范列表。这相当于我们通常在 C 语言中不带指针传递函数。

或者，我可以把实物清单寄给 Lina，让她在上面加上自己的名字和喜欢的颜色，然后寄回给我。这将确保我仅有的文件是完整的列表。这相当于将变量的地址传递给函数，或者通过引用传递。

## 参照传递:代码

> 同样，如果你能跟上，请做。否则，只是通读，我将分享程序的输出。

为了说明传递变量值和变量引用之间的区别，请看这个简单的程序:

```
#include <stdio.h>  
void add_five(int);
void ptr_add_five(int*);

int main() {
  int var = 10;

  // We pass the var variable by
  // reference. Note that nothing
  // happens to the value of var.
  printf("The value of var before the function is %i\n", var);
  add_five(var);
  printf("The value of var after the function is %i\n", var);

  // Now we will pass the variable
  // by reference. Note that this time
  // the actual value changes.
  printf("The value of var before the function is %i\n", var);
  ptr_add_five(&var);
  printf("The value of var after the function is %i\n", var);

  return 0;
}

void add_five(int x) {
  x += 5;
  printf("The add_five function fired\n");
}

void ptr_add_five(int *x) {
  *x += 5;
  printf("The ptr_add_five function fired\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是输出:

```
The value of var before the function is 10
The add_five function fired
The value of var after the function is 10
The value of var before the function is 10
The ptr_add_five function fired
The value of var after the function is 15 
```

Enter fullscreen mode Exit fullscreen mode

明白我的意思了吗？我们有两个相对相同的函数，每个函数都给传递的参数加 5。这个函数通过抓取一些独立的内存地址，给它们赋值 5 来创建一个新值，然后我们就再也看不到它了。在这个函数中，x 将是 15，是的，但是一旦我们回到`main`函数，这个变量就几乎消失了。

取指针的函数有我们想要操作的变量的地址。它获取地址，找到该地址的值，然后加 5。这就是为什么当程序返回到`main`函数时，变量的值发生了变化。

大多数情况下，当 C 向函数传递变量时，它是通过值来传递的。但是，数组不是。事实上，C 处理将数组传递给函数的方式是将数组的第一个值的地址传递给函数，然后函数将使用*指针算法*来计算如何遍历数组，这很好地引出了第 3 部分。