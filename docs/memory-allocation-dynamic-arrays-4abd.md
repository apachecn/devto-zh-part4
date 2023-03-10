# 内存分配和动态数组

> 原文：<https://dev.to/giladri/memory-allocation-dynamic-arrays-4abd>

让我们从挑战的解决方案开始:

```
#include <stdio.h> 
void swap(int * x, int * y) {
    *x += *y;     // x == 13
    *y = *x - *y; // y == 10
    *x -= *y;     // x == 3
}

int main() {
    int x = 10;
    int y = 3;
    swap(&x, &y);
    printf("x=%d, y=%d", x, y);
    // Success
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在到我们的主题:内存分配，以及如何初始化动态数组。

正如你所记得的，在上一篇文章中，我们学习了内存和指针。在这篇文章中，我们将更深入地探讨记忆这个主题。我们电脑的内存分为两种:**栈**和**堆**。

### **栈**

我们已经熟悉了堆栈:到目前为止，每当我们创建了一个变量或函数，我们就在堆栈上分配它。更准确地说:只有当**我们确切知道在编译过程**之前需要分配多少空间时，我们才能在堆栈上分配东西。这意味着堆栈仅用于**静态内存分配**。(还记得我们说过的静态数组吗？)

当它们的块范围结束时，堆栈上分配的空间被释放(“清除”)。例如，当函数自动结束时，函数变量被删除。(更准确地说，它们实际上并没有被删除，而是“计算机”只是让我们的程序覆盖了它们的空间——“计算机停止保持它们的位置，因此未来的内存分配可以覆盖它们”)。

### **堆**

新的内存类型我们将在本帖中讨论。我们可以在运行时在堆**上分配我们想要的任意大小的空间**(只要有足够的剩余空间)。此外，我们可以随时重新分配空间或释放(“清除”)空间。注意，这种类型的内存不会被释放，除非我们让它这样做。如果我们想长时间保存某个东西的值(例如，比函数范围更长)，这是一件**好事**，但也是一件**坏事**，因为我们必须记住释放这些空间，否则我们会有麻烦。在某些时候，我们可能会耗尽内存，我们的程序将崩溃。在我们的例子中，它听起来并不那么具有威胁性-“哦，不，我们愚蠢的程序可能会崩溃”-但是当它涉及到一个大银行的程序时，例如，每一秒钟的停机时间都会给银行造成数百万美元的损失，这真的很可怕！因此，请记住在合适的时间释放你在堆上分配的空间——不要太早也不要太晚。

### **内存分配**

好，让我们看看如何在堆上分配一个变量:

```
#include <malloc.h> 
int * pointer = (int *) malloc(sizeof(int)); 
```

Enter fullscreen mode Exit fullscreen mode

或者，一般来说:

```
#include <malloc.h> 
[type] [pointer_name] = ([type]) malloc([number_of_bytes_we_want]); 
```

Enter fullscreen mode Exit fullscreen mode

我们把这个分成几部分:
第一部分，**【类型】【指针名称】**我们只是初始化一个指针，它指向我们将要在堆上分配的空间。下一部分，在“=”后面的部分是有趣的部分。首先，我们只是转换 **`malloc`** 函数输出的类型。在 C 语言中，如果在变量前放一个括号，就可以转换变量的类型。例如:

```
int x = 10;
double y = (double) x; 
```

Enter fullscreen mode Exit fullscreen mode

**`malloc`** 函数的输出类型为(void *)。这种类型就像一个小丑类型，你可以把它转换成你想要的任何类型。因此，因为我们希望它匹配指针，所以我们将它转换为(int *)。换句话说，我们只需要转换输出类型，使其与指针类型相匹配。

而最后一部分， **`malloc`** 输入。 **`malloc`** 函数接收一个**大小的**并返回一个**指针，指向这个**大小**的已分配空间**。我们可以选择任何我们想要的大小，但是通常，我们使用 **`sizeof`** 函数，该函数获取一种类型的变量(例如 **int** )并返回它的字节大小。通常情况下， **int** 的大小为 4 字节，但这可能会随着技术的进步而改变，也可能因操作系统、编译器和各种处理器等而异。因此，我们更喜欢使用 **`sizeof`** ，这样，我们在获取变量类型的大小时完全没有问题。

### **动态数组**

最后，让我们看看如何使用 **`malloc`** 函数:
初始化一个动态数组

```
#include <stdio.h>
#include <malloc.h> 
#define SIZE 10 
int main() {
    // Allocates an array of size SIZE
    int * grades = (int *) malloc(SIZE * sizeof(int));
    // Frees the array
    free(grades);
    // Success
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们刚刚将变量类型的大小乘以了数组所需的元素数量。

上例中的 **`free`** 函数就是我们释放已分配内存的方法。我们只需要把指向内存分配的指针作为输入发送给它。

为了总结这篇文章，让我们以一种更好的方式重写来自[静态数组文章](https://dev.to/giladri/static-arrays-29m2)(一个可以保存学生成绩并计算平均分的应用程序)的例子:

```
#include <stdio.h>
#include <malloc.h> 
#define START_SIZE 2 
void getGrades(int * grades, int * index, int * size) {
    int grade;
    printf("Insert the grades. In order to stop, please enter -1\n");
    do {
        printf("Grade #%d: ", *index + 1);
        scanf("%d", &grade);
        // Checks the grade
        if (grade >= 0 && grade <= 100) {
            // Checks if there is enough space in the array
            if (*index >= *size) {
                // There isn't, doubles the size of the array
                *size *= 2;
                realloc(grades, *size * sizeof(int));
                printf("Re-allocated the array to size: %d\n", *size);
            }
            // Inserts the grade into the array and increases the index by 1
            grades[*index] = grade;
            *index += 1;
        } else if (grade == -1) {
            // Stops
            printf("Thank you!\n");
        } else {
            // Error
            printf("Invalid grade, please try again...\n");
        }
    } while (grade != -1);
}

double calcAverage(int * grades, int index) {
    int i;
    int sum = 0;
    for (i = 0; i < index; i++) {
        sum += grades[i];
    }
    return (double) sum / (double) index;
}

int main() {
    int index = 0;
    int size = START_SIZE;
    // Allocates an array of size 'START_SIZE'
    int * grades = (int *) malloc(size * sizeof(int));
    // Gets the grades from the user as input
    getGrades(grades, &index, &size);
    // Prints the average
    printf("Your average is: %.2f\n", calcAverage(grades, index));
    // Frees the array
    free(grades);
    // Success
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

控制台示例:

```
Insert the grades. In order to stop, please enter -1
Grade #1: 100
Grade #2: 90
Grade #3: 95
Re-allocated the array to size: 4
Grade #4: 100
Grade #5: 87
Re-allocated the array to size: 8
Grade #6: 94
Grade #7: 93
Grade #8: 86
Grade #9: 99
Re-allocated the array to size: 16
Grade #10: 88
Grade #11: -1
Thank you!
Your average is: 93.20 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们使用了 **`realloc`** 函数，该函数获得一个指针和一个新的大小，并重新分配一个我们想要的新大小的空间(并复制旧空间中的所有值)。如果你想知道为什么我们**每次都将**的尺寸翻倍，而不仅仅是增加一些空间(例如，尺寸+ 10)，你可以阅读 Nicholas Nethercote 在 2014 年的这篇简短的[帖子](https://blog.mozilla.org/nnethercote/2014/11/04/please-grow-your-buffers-exponentially/)。

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德