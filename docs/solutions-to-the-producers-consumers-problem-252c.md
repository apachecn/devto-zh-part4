# 生产者-消费者问题的解决方案

> 原文：<https://dev.to/renegadecoder94/solutions-to-the-producers-consumers-problem-252c>

撇开读者-作者问题不谈，让我们来谈谈另一个同步问题:生产者-消费者。

## 生产者——消费者问题概述

与读者和作者不同，生产者和消费者不争夺资源。相反，它们相互依赖来平衡一些共享资源。例如，生产者可能正在创建添加到队列中的消息，而消费者负责处理这些消息。

更正式的说法是，生产者和消费者共用一个队列。当队列包含商品时，有且只有一个消费者应该要求处理第一个商品。具体来说，我们不希望多个消费者处理同一个商品。同样，只要队列未满，生产者就可以向队列中添加项目。

也不同于读者-作者问题，生产者-消费者问题没有任何排列。因为没有任何对资源的竞争，所以没有必要优先考虑任何进程。因此，对于每个同步机制，我们只需经历一个解决方案。在未来，我们将经历一些更复杂的生产者-消费者问题(例如，可重复使用的资源等)。).

在本文中，我们将研究生产者-消费者问题的两种解决方案:一种使用信号量，另一种使用监视器。像往常一样，例子是从俄亥俄州立大学的 CSE 6431 课堂笔记中借来的:

*   [OSU CSE 6431 进程同步第二部分](https://therenegadecoder.com/wp-content/uploads/2019/07/osu-cse-6431-process-synchronization-part-2.pdf?x24663)(信号量)
*   [OSU CSE 6431 进程同步第三部分](https://therenegadecoder.com/wp-content/uploads/2019/07/osu-cse-6431-process-synchronization-part-3.pdf?x24663)(信号量)
*   [OSU CSE 6431 过程同步第 4 部分](https://therenegadecoder.com/wp-content/uploads/2019/07/osu-cse-6431-process-synchronization-part-4.pdf?x24663)(监视器)

当然，所有分析严格来说都是我自己的。

## 生产者-消费者问题解决方案

在这一节中，我们将实现生产者-消费者问题的几个解决方案。特别是，我们将研究信号量解决方案，并将其与监视器解决方案进行对比。我将再次使用模仿 Python 的伪代码。

### 信号量

为了实现这个解决方案，我们需要三个信号量:`mutex`、`prod_mutex`、`cons_mutex`。换句话说，我们需要一个二进制信号量来全局保护队列和一些共享变量。此外，我们将需要一对计数信号量来确保相同进程集之间的互斥。顺便说一句，额外的互斥对于消费者和生产者之间的通信是有用的。

最重要的是，我们需要引入一些共享变量，比如队列中元素的数量`count`和队列最大大小的常量`SIZE`。

```
procedure producer(): 
  P(mutex) 
  if count = SIZE: 
    V(mutex) 
    P(prod_mutex) 
    P(mutex) 
  else: 
    P(prod_mutex) 
  count++ 
  # Add item to queue 
  V(cons_mutex) 
  V(mutex)

procedure consumer(): 
  P(mutex) 
  if count = 0: 
    V(mutex) 
    P(cons_mutex) 
    P(mutex) 
  else: 
    P(cons_mutex) 
  count-- 
  # Remove item from queue 
  V(prod_mutex) 
  V(mutex) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，一次只能运行一个进程，除非在下面描述的特殊情况下。在生产者的情况下，它将检查队列中是否有空间。如果有，它将尝试获取`prod_mutex`，产生一个项目，并增加`count`。否则，它将释放`mutex`，直到队列中有空位。当该说的都说了，该做的都做了，生产者让任何等待的消费者知道至少有一个商品在队列中等待。

同时，消费者将检查队列中是否有任何东西。如果有，它试图获取`cons_mutex`，消耗一个项目，并递减`count`。否则，它将释放`mutex`,等待队列中有东西。当该说的都说了，该做的都做了，消费者让任何等待的生产者知道队列中至少有一个空位。

请注意这两个过程是如何完美地相互映射的。因此，你应该问一个后续问题:这个解决方案容易出现死锁吗？事实上，我也问过自己这个问题。有没有可能一个制作人抢了`mutex`而另一个制作人有了`prod_mutex`？毕竟，一个生产者需要两者来生产。

这是我想象的场景。假设共享队列已满，一个新的制片人(P1)出现了。到那时，P1 将不可避免地发现自己卡在了队列中。这时，一个新的消费者(C1)出现了。工作进行到一半时，另一个制片人出现了(P2)。C1 完成后，将`prod_mutex`移交给 P1，同时将`mutex`移交给 P2。我们现在陷入了僵局。

事实证明，我提供的解决方案只适用于单个消费者和单个生产者。

### 监视器

通常情况下，monitor 解决方案更容易实现。一般来说，我们只需要定义一个共享队列和一个共享计数变量。此外，我们需要维护两个类似于计数信号量的条件变量:can_consume 和 can_produce。最后，我们将定义两个过程:生产者和消费者。

```
procedure producer(): 
  if count = SIZE: 
    can_produce.wait 
  count++ 
  # Add item to queue 
  can_consume.signal

procedure consumer(): 
  if count = 0: 
    can_consume.wait 
  count-- 
  # Remove item from queue 
  can_produce.signal 
```

Enter fullscreen mode Exit fullscreen mode

由于监视器过程是序列化的，我们只需要担心一次运行一个过程。当一个生成器出现时，它会检查我们的共享队列中有多少项。如果队列已满，生成器将等待。否则，生产者将向队列中添加一个项目，并通知消费者消费它。

同样，当消费者出现时，它会检查是否有东西可以消费。如果没有，那就等。否则，它将消耗队列中的一项，并向生产者发出信号，让它知道还有空间。

和上一个解决方案一样，我很好奇是否有死锁的机会，但是我不相信有。我们之前遇到的问题与两个进程因等待对方而停滞不前有关。在这种情况下，我认为那是不可能的。出于理智的考虑，我试图重现之前描述的问题，但由于这个解决方案的结构方式，它根本不存在。

## 想了解更多？

在这一点上，关于生产者和消费者的问题，我要说的就是这些。当然，我想继续学习一些其他的概念，比如下一个要讨论的进程间通信。

同时，如果你喜欢这篇文章，分享一下吧！另外，如果你开始在 pat reon 上支持我，或者至少在我的邮件列表上跳转到[，我会很感激。](https://newsletter.therenegadecoder.com/)

另外，我有很多文章，我想你可能会喜欢，包括:

*   [读者-作者问题的解决方案](https://dev.to/renegadecoder94/solutions-to-the-readers-writers-problem-374-temp-slug-274969)
*   [了解流程同步](https://dev.to/renegadecoder94/understanding-process-synchronization-18jd)
*   [复制可变数据类型时要小心](https://dev.to/renegadecoder94/be-careful-when-copying-mutable-data-types-2loa)

无论如何，再次感谢你的支持！早点回来。

关于生产者-消费者问题的解决方案的帖子最先出现在叛逆的编码者 T2 的网站上。