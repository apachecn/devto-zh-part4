# c 指针 101:第 1 部分，简单介绍

> 原文：<https://dev.to/erikwhiting88/c-pointers-101-part-1-a-gentle-introduction-3an6>

如果 C 语言的指针特性曾经威胁过你，你并不孤单。许多初学这门语言的程序员被指针的概念绊倒了，但我相信这是因为指针教得不好。

在这个系列中，我们将慢慢地学习指针，只要不被它们吓倒就行。这是第 1 部分，一个温和的介绍。第 2 部分将讨论通过引用传递，第 3 部分将触及令人生畏的“指针算法”准备好开始了吗？我确实是。

# 什么是指针？

在 C 语言中，指针“指向”保存一个值的内存区域的地址。那是什么意思？好吧，现在，没关系。让我们先从例子开始，然后我们再深入一点。

> 如果您的环境设置为编写和编译 C，请继续运行它，否则请继续，我将分享代码示例的输出。

# 我的朋友和他们的地址

我有一个朋友，他的名字叫“一”他住在我电脑程序里的房子里。让我们找出他的地址。

```
#include <stdio.h>  int main() {
  int friend_one = 1;
  printf("My friend %i lives at address %p \n", friend_one, &friend_one);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们编译并运行，看看会发生什么:

```
$ gcc -o pointersPart1 pointersPart1.c
$ ./pointersPart1
My friend 1 lives at address 0x7ffc52e56554 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你跟着走，几乎可以保证你的地址会和我的不一样，不要担心，这很正常。

## 那是什么？

现在，让我们坚持使用地址类比。

我们的朋友`int friend_one`需要运行这个程序，所以计算机给他分配了一些土地。那块土地的地址是`0x7ffc52e56554`(从技术上来说，地址“开始”于此，我们将在后面介绍)。

每当程序需要我们的伙伴`friend_one`时，计算机就知道在那个地址去哪里找他。现在，你也知道去哪里找他了，因为&符号(`&`)是你获取变量地址的方式。

再来看另一个例子:

```
#include <stdio.h>  int main() {
  int friend_one = 1;
  int friend_two = 2;

  int *address_one = &friend_one;
  int *address_two = &friend_two;

  printf("My friend_one(%i) lives at %p\n", friend_one, address_one);
  printf("My friend_two(%i) lives at %p\n", friend_two, address_two);

  printf("Who lives at %p? %i does\n", address_one, *address_one);
  printf("Who lives at %p? %i does\n", address_two, *address_two);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

编译运行:

```
$ gcc -o pointersPart1_2 pointersPart1_2.c
$ ./pointersPart1_2
My friend_one(1) lives at 0x7ffc763f2440
My friend_two(2) lives at 0x7ffc763f2444
Who lives at 0x7ffc763f2440? 1 does
Who lives at 0x7ffc763f2444? 2 does 
```

Enter fullscreen mode Exit fullscreen mode

让我们抛开类比，谈谈这里到底发生了什么。我们声明两个整数变量，`friend_one`和`friend_two`，并分别给它们赋值 1 和 2。现在，下一部分`int *address_one`是你如何为一个*整数*变量声明一个*指针*。然后我们把这个指针的值设置为`&friend_one`。

所以现在，指针变量**指向**这些地址。然后，我们想实际看到那个值。在 C 中，指针的字符串插值操作符是`%p`，当我们将`address_one`放入那个`printf`函数中时，我们得到了实际的地址。

然而，真正神奇的是最后两行:

```
printf("Who lives at %p? %i does\n", address_one, *address_one);
printf("Who lives at %p? %i does\n", address_two, *address_two); 
```

Enter fullscreen mode Exit fullscreen mode

我们知道指针指向地址，但是如果我们想让指针告诉我们地址里有什么呢？然后我们使用`*pointer_variable_name`来获取信息。

## 走之前有点理论

很快的，让我们讨论一下内存分配和堆栈与堆。当你的程序启动时，计算机分配一部分内存给程序运行。分配给程序的内存是程序的堆栈，计算机上剩余的内存称为堆。

其中每个地址代表一个字节，因此如果您的计算机有 4GB 的 RAM，它就有 4 x 1，073，741，824 个唯一地址可供使用。一个整型变量通常占用 4 个字节，这就是为什么上面的两个变量地址相隔 4 个字节。还记得我说过整数的地址其实*从 0x7ffc52e56554 开始*吗？这是因为该整数占用了
0x 7 ffc 52 e 56554
0x 7 ffc 52 e 56555
0x 7 ffc 52 e 56556
和 0x7ffc52e56557

这就是为什么大多数编译语言都是静态类型的(声明可变数据类型的语言)。编译器(或其等价物)必须知道程序将占用多少地址，这样计算机才能相应地分配。

在 C 和其他允许直接内存操作的语言中，你的程序并不局限于分配给它的内存，它可以借用堆内存。在 C 语言中，`malloc()`用来借用堆内存，`free()`把它还给计算机。同样，在 C++中，`new`和`delete`关键字的操作类似。

如果你听说过编程语言中的“垃圾收集”(或酷孩子的`gc`)，那指的是一种语言在内存用完时将程序使用的内存释放回计算机的能力。在像 C 和 C++这样的语言中，你可以控制释放你所借用的内存，gc 由你决定。有时会出现这样的错误:一旦不再需要内存，就不会将其释放回计算机，这就是所谓的“内存泄漏”。

反正我已经让你烦够了。看看下一篇文章，看看我们可以用指针做些什么。