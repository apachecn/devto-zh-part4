# 指针 101:第 3 部分，指针算法

> 原文：<https://dev.to/erikwhiting88/c-pointers-101-part-3-pointer-arithmetic-30n6>

在第 1 部分中，我们学习了指针的基础知识。在第 2 部分中，我们学习了通过值或引用传递变量的含义。还记得我在第 2 部分说过，当 C 把一个数组传递给一个函数时，它把指针传递给数组的第一个元素，然后指针算法处理剩下的部分吗？让我们弄清楚我的意思。

# 什么是指针算术

简而言之，指针算法是根据数组的数据类型找出数组中的下一项可能在哪里的数学方法。

## 工作原理

每个变量都要占用计算机的一些内存。例如，整数通常被分配 4 个字节。一些编译器分配 2 个字节，但是在前面的例子中，我的整数是 4，所以让我们继续。基本上，对于 4 个地址，一个整数有这么多工作:

```
0000 0000  0000 0000  0000 0000  0000 0000 
```

Enter fullscreen mode Exit fullscreen mode

如果**所有的**位都被打开，数字将是 2，147，483，647，因此是一个 32 位(4 字节)整数所能达到的最大数字。

所以，如果我们有一个 3 个整数的数组，我们知道这个数组将占用 12 个字节。我们还知道，如果数组中的第一个值位于地址 0x7ffc763f2444，那么第二个值将位于 0x7ffc763f2448，最后一个值将从 0x7ffc763f244c 开始(因为这是十六进制的)。

让我们继续深入一些解释这一点的代码:

```
#include <stdio.h>  int main() {
  int array[3] = {10, 20, 30};

  printf("Address of first element (the number 10): %p\n", &array[0]);

  // If we add 1 to the pointer, it should
  // show us the value of array[1], the
  // number 20

  int *first_addr_in_array = &array[0];
  int *second_addr_in_array = first_addr_in_array + 1;

  printf("Second array value is: %i\n", *second_addr_in_array);
  printf("Subtracting the second and first array addresses: \n");
  printf("%ld\n", second_addr_in_array - first_addr_in_array);

  // Finally, the address of the third
  // element in the array, array[2],
  // minus the address of array[1] will
  // be 1\. Also, the value will be 30

  int *third_addr_in_array = &array[2];
  int difference_between_2nd_and_3rd = third_addr_in_array - second_addr_in_array;
  printf("The difference between 2nd and 3rd array addresses: \n");
  printf("%i\n", difference_between_2nd_and_3rd);

  printf("The address of the 3rd element in the array: ");
  printf("%p\n", third_addr_in_array);
  printf("The value of the 3rd element in the array: ");
  printf("%i\n", *third_addr_in_array);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

编译并运行，下面是我得到的:

```
Address of first element (the number 10): 0x7ffdd20386c
Second array value is: 20
Subtracting the second and first array addresses:
1
The difference between 2nd and 3rd array addresses:
1
The address of the 3rd element in the array: 0x7ffdd2038d74 
```

Enter fullscreen mode Exit fullscreen mode

> 请原谅我在 1 号和 1 号之间的变调

我们到底在这里做了什么？

首先，我们创建了一个值为 10、20 和 30 的数组。然后，我们得到了数组中第一个元素的地址。这是一个函数将得到的值，如果你将这个数组作为参数传递给它。

然后，我们在地址上加 1，看看会有什么值。正如我们所料，我们得到了 20。c 足够聪明，知道这是一个整数数组，我们加 1，我们实际上是把数据类型的大小加到地址上，以得到下一个值。对于整数数组，这些值相隔 4 个地址。如果这是一个由`short`组成的数组，那么这些元素将相隔 2 个地址。

然后，只是为了好玩，我们减去一些内存地址，看看我们是否仍然得到我们认为我们会得到的(我们确实得到了)。

这是指针算法背后的基本思想，真的没那么难。

# 系列总结

我真的希望这个系列足够短，能够抓住你的注意力，足够丰富，能够帮助你更好地理解这个基本概念。你可能在你的整个编程生涯中从未想过指针，但它们将永远存在。多了解他们一点也无妨。如有任何问题，欢迎在此评论或在 Twitter 上@我。