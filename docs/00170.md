# 多线程编程简介

> 原文：<https://dev.to/chetchopra/a-brief-intro-to-multi-threaded-programming-546j>

当然，在我们开始讨论多线程程序之前，我们需要一些背景信息。尽管有很多关于这个概念的背景信息，我们将只讨论最基本的。

### 什么是线程？

线程是程序中连续的控制流。所以它本质上是一个由调度程序管理的程序指令序列，这样它们就可以被执行。

### 什么是调度器？

现在不要担心它。只要知道它是操作系统的一部分。如果你想了解更多，参考资料部分有一个链接。

假设您在一个超线程环境中使用一个具有多个物理内核或多个虚拟内核的系统。多线程是一种告诉你的操作系统这些是不同的线程或指令块，我想在我的电脑上运行。操作系统(调度程序)负责选择哪个线程获得 CPU 内核的时间，无论它们是物理的还是虚拟的。因此，如果你有一个在多线程上运行的程序，它提出了一些有趣的问题和一些非常强大的优势。

### 一期

以下面相同的代码为例。

### 错误的多线程代码

```
# include <stdio.h>
# include <stdlib.h>
# include <pthread.h>
# include <unistd.h>
volatile long int a = 0;
void threadOne(void *arg) {
  int i;
  for (i = 1; i < 500000; i++) {
    a = a + i;
  }
}
void threadTwo(void *arg) {
  int i;
  for (i = 500000; i <= 1000000; i++) {
    a = a + i;
  }
}
int main (int argc, char **argv) {
  pthread_t one, two;
  int i;
  pthread_create(&one, NULL, (void*)&threadOne, NULL);
  pthread_create(&two, NULL, (void*)&threadTwo, NULL);
  pthread_join(one, NULL);
  pthread_join(two, NULL);
  printf("%ld\n", a);
} 
```

这个程序所做的就是把从 1 到 190009000 的所有数字相加。但是问题是它使用了两个执行线程。因此，一个线程将从 1 到 500，000 的数字相加，另一个线程将从 500，000 到 1，000，000 的数字相加。

让我们多次运行这段代码并观察。

```
Run #1 Output - 370752617319
Run #2 Output - 336751282108
Run #3 Output - 250846382315
Run #4 Output - 314774608001 
```

嗯…为什么我们每次运行这段代码都会得到不同的答案？理论上，与使用单线程相比，我们应该以一半的量获得正确的答案。

这是另一个程序，它做同样的事情，但是只使用了一个线程。

### 单线程

```
# include <stdio.h>
# include <stdlib.h>
# include <unistd.h>
volatile long int a;
int main (int argc, char **argv) {
  int i;
  a = 0;
  for (i = 1; i <= 1000000; i++) {
    a = a + i;
  } 
  printf("%ld\n", a);
} 
```

正如所料，我们每次都得到相同的值。那么多线程程序在做什么让它产生不同的不正确答案呢？

每个线程都从内存中加载变量 A，制作一个本地副本，向其中添加一个值，然后将其存储回 A。问题是两个线程都使用全局变量 A 来累加它们的数字，但它们没有同步工作。它们可以同时加载变量 A，向其添加一个值，然后将其存储回 A。根据哪一个先将值存储回 A，一个更新将被另一个覆盖。

这个问题的解决方案是同步这两个线程，这样它们就不会试图同时操作/访问 A。一种方法是使用互斥体(互斥)。你可以用代币做类比。假设现在每个线程只有在拥有令牌的情况下才能访问。这意味着，如果线程一拥有令牌并正在使用，那么线程二就不能访问，并被迫等待，直到线程一释放令牌。

让我们看一些代码

### 更好但仍然不好的多线程代码

```
# include <stdio.h>
# include <stdlib.h>
# include <pthread.h>
# include <unistd.h>
pthread_mutex_t a_mutex = PTHREAD_MUTEX_INITIALIZER;
volatile long int a = 0;
void threadOne(void *arg) {
  int i;
for (i = 1; i < 500000; i++) {
    pthread_mutex_lock(&a_mutex);
    a = a + i;
    pthread_mutex_unlock(&a_mutex);
  }
}
void threadTwo(void *arg) {
  int i;
  for (i = 500000; i <= 1000000; i++) {
    pthread_mutex_lock(&a_mutex);
    a = a + i;
    pthread_mutex_unlock(&a_mutex);
  } 
}
int main (int argc, char **argv) {
  pthread_t one, two;
  int i;
  pthread_create(&one, NULL, (void*)&threadOne, NULL);
  pthread_create(&two, NULL, (void*)&threadTwo, NULL);
  pthread_join(one, NULL);
  pthread_join(two, NULL);
  printf("%ld\n", a);
}
Run #1 Output - 500000500000
Run #2 Output - 500000500000
Run #3 Output - 500000500000
Run #4 Output - 500000500000 
```

您可以看到有一些新的行锁定和解锁资源，防止被访问。现在我们得到了正确的输出！但是仍然有一个问题，因为这样使用线程是没有意义的。这是没有意义的，因为当线程一正在处理一个线程时，线程二被迫等待，直到线程一释放资源。理论上，这和使用单线程一样慢。

是时候解决它了！

### 优势

### 好的多线程代码

```
# include <stdio.h>
# include <stdlib.h>
# include <pthread.h>
# include <unistd.h>
pthread_mutex_t a_mutex = PTHREAD_MUTEX_INITIALIZER;
volatile long int a = 0;
void threadOne(void *arg) {
  int i;
  long int localA = 0;
  for (i = 1; i < 500000; i++) {
    localA = localA + i;
  }
  pthread_mutex_lock(&a_mutex);
  a = a + localA;
  pthread_mutex_unlock(&a_mutex);
}
void threadTwo(void *arg) {
  int i;
  long int localA = 0;
  for (i = 500000; i <= 1000000; i++) {
    localA = localA + i;
  }
  pthread_mutex_lock(&a_mutex);
  a = a + localA;
  pthread_mutex_unlock(&a_mutex);
}
int main (int argc, char **argv) {
  pthread_t one, two;
  int i;
  pthread_create(&one, NULL, (void*)&threadOne, NULL);
  pthread_create(&two, NULL, (void*)&threadTwo, NULL);
  pthread_join(one, NULL);
  pthread_join(two, NULL);
  printf("%ld\n", a);
} 
```

在这段代码中，我们在每个函数中都使用了一个名为 localA 的局部变量。我们这样做是为了让线程 1 将从 1 到 500，000 的数字添加到自己的 localA 中，同时线程 2 将从 500，000 到 1，000，000 的数字添加到自己的 localA 中。在一个线程完成添加后，它将锁定 A，更新它，然后解锁它，以便下一个线程可以使用它。所以现在我们已经大大减少了一个线程等待另一个线程的时间。理论上，这应该比使用单线程运行快一倍。

#### 参考文献

这个博客是基于 Youtube 上一个由 Computerphile 制作的很棒的视频

[https://youtu.be/7ENFeb-J75k](https://youtu.be/7ENFeb-J75k)

[https://pdfs . semantic scholar . org/a23b/22 FB 7 BC 0286101 BDF 4794 E1 e 61 bcad 2 fa 896 . pdf](https://pdfs.semanticscholar.org/a23b/22fb7bc0286101bdf4794e1e61bcad2fa896.pdf)

[https://en . Wikipedia . org/wiki/Scheduling _(计算)](https://en.wikipedia.org/wiki/Scheduling_(computing))