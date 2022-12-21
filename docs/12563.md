# 读者-作者问题的解决方案

> 原文：<https://dev.to/renegadecoder94/solutions-to-the-readers-writers-problem-1147>

如果你一直在关注的话，我现在正在准备博士资格考试。作为考试的一部分，我需要复习一下我的操作系统知识。之前，我介绍了一些进程同步机制，现在我想开始深入研究如何使用这些机制来解决真正的问题，比如读者-作者问题。

## 读者——作家问题概述

在并发中，一个常见的挑战是平衡谁可以访问共享变量。特别是，我们应该围绕共享内存访问设置什么样的规则，以便操作既安全又高效？

更具体地说，读取器-写入器问题关注于与平衡线程或进程相关的挑战，这些线程或进程希望从共享内存位置读取或向其写入。换句话说，我们如何调度线程，以获得我们想要的结果(例如，读者优先级、作者优先级等)。)?

在本文中，我们将研究一些读者-作者场景，并尝试使用我们上次讨论过的一些机制(如信号量和监视器)来解决它们。明确地说，本文中的所有代码都是类似 Python 的伪代码，除了利用 Java 并发特性的潜在示例。所有的例子都是从俄亥俄州立大学的 CSE 6431 课堂笔记中借来的:

*   [OSU CSE 6431 进程同步第二部分](https://therenegadecoder.com/wp-content/uploads/2019/07/osu-cse-6431-process-synchronization-part-2.pdf?x24663)(信号量)
*   [OSU CSE 6431 进程同步第三部分](https://therenegadecoder.com/wp-content/uploads/2019/07/osu-cse-6431-process-synchronization-part-3.pdf?x24663)(信号量)
*   [OSU CSE 6431 过程同步第 4 部分](https://therenegadecoder.com/wp-content/uploads/2019/07/osu-cse-6431-process-synchronization-part-4.pdf?x24663)(监视器)

自然所有的分析都是我自己的。

## 读者-作者问题解答

在这一节中，我们将研究使用不同的进程同步机制来解决读者-作者问题的各种解决方案。每个子部分包含不同类型的解决方案，这些解决方案随后由机制组织成子部分。

### 连环解

协调几个读者和作者可能是一个挑战。解决这个问题的一个方法是组织读写操作，使得一次只有一个进程可以访问共享内存。使用这种解决方案，很容易实现正确性。然而，由于没有进程优先级的机制，效率无法得到保证。

#### 信号量

使用信号量，我们可以定义两个过程:读取器和写入器。每个程序都由同一个互斥体保护:

```
procedure reader(): 
  P(mutex) 
  <read> 
  V(mutex)

procedure writer(): 
  P(mutex) 
  <write> 
  V(mutex) 
```

Enter fullscreen mode Exit fullscreen mode

#### 监视器

使用监视器，可以在监视器内部定义共享资源。然后，我们设置了两个过程:reader 和 writer。由于监视器资源是受保护的，我们可以随意调用这些过程，而不用担心任何竞争条件:

```
procedure reader(): 
  <read>

procedure writer(): 
  <write> 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，这将完成工作，但它并不理想。

### 并发阅读器解决方案

由于读操作对共享资源没有影响，我们通常允许它们并发发生。换句话说，如果两个读者碰巧在没有东西写入我们的共享资源时出现，我们应该让两个读者都读。

#### 信号量

对于信号量，读取过程必须分为三个阶段:

1.  检查是否可以安全阅读(即当前没有写入程序)
2.  阅读
3.  检查写入是否安全(即不再有读者)

此外，我们必须引入一个额外的互斥体，这样我们就可以开始跟踪一个队列中的读者和另一个队列中的写入者。除此之外，我们还必须引入一个共享变量来跟踪活跃读者的数量:

```
procedure reader(): 
  # Stage 1 
  P(reader_mutex) 
  if readers = 0: 
    P(writer_mutex) 
  readers++ 
  V(reader_mutex) 

  # Stage 2 
  <read> 

  # Stage 3 
  P(reader_mutex) 
  readers-- 
  if readers = 0: 
    V(writer_mutex) 
  V(reader_mutex)

procedure writer(): 
  P(writer_mutex) 
  <write> 
  V(writer_mutex) 
```

Enter fullscreen mode Exit fullscreen mode

注意`readers`变量是如何被用来在读者和作者之间建立联系的。如果一个读者意识到它是第一个，它需要阻止写者互斥体以避免任何共享内存访问问题。如果成功的话，读者会一直持有这个互斥体，直到没有读者了。

如果由于某种原因，第一个读者没有得到写者互斥体，那么它在等待的同时也持有读者互斥体。换句话说，在第一个读者获得写者互斥体之前，所有的读者都是冻结的。

#### 监视器

像信号量一样，monitors 解决方案对并发阅读器访问需要一些修改。特别是，我们需要将 reader 过程分成两个过程。这样，我们可以从监视器中删除读取功能，这样读取就不会连续发生。此外，我们需要像前面一样定义一个共享的 readers 变量。

也就是说，显示器解决方案略有不同。我们没有显式地维护两个队列，而是使用名为 writer:
的条件变量来维护一个队列

```
procedure begin_read(): 
  readers++

# Reading occurs between these procedures 
procedure end_read(): 
  readers-- 
  if readers = 0: 
    writer.signal

procedure write(): 
  if readers > 0: 
    writer.wait 
  <write> 
  writer.signal 
```

Enter fullscreen mode Exit fullscreen mode

因为一次只有一个进程可以执行一个监控过程，所以我们不必担心这方面的任何竞争情况。然而，由于共享资源(即一个文件)不受我们的监视器保护，我们不得不在并发读取期间防止写操作。为了做到这一点，我们引入了一个写者条件变量，如果有任何读者，我们就用它来拖延写者。

否则，对于读者来说，生活是相当简单的。如果我们能够读取(即没有被监视器阻止)，我们在读取之前增加读取器的数量。在阅读之后，我们减少读者的数量。如果当前读取器是最后一个读取器，它将控制权交给任何等待的写入器。

### 读者优先解决

本质上，并发阅读器解决方案的工作原理是优先考虑最先到达的进程。在某种程度上，读者获得了优先权。毕竟，只要有读者，就会继续读下去。然而，作者从不把控制权交给读者，所以有可能作者会让读者饿一段时间(反之亦然)。

如果我们想要一个真正的读者优先的解决方案，我们必须研究一种机制，让作者在完成后将控制权交给读者。

#### 信号量

为了用信号量精心设计一个读者优先的解决方案，我们必须引入另一个信号量:

```
procedure reader(): 
  # Stage 1 
  P(reader_mutex) 
  if readers = 0: 
    P(writer_mutex) 
  readers++ 
  V(reader_mutex) 

  # Stage 2 
  <read> 

  # Stage 3 
  P(reader_mutex) 
  readers-- 
  if readers = 0: 
    V(writer_mutex) 
  V(reader_mutex)

procedure writer(): 
  P(sync_mutex) 
  P(writer_mutex) 
  <write> 
  V(writer_mutex) 
  V(sync_mutex) 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，这个解决方案与并发读取器解决方案非常相似。当然，不同之处在于利用新互斥体的编写器过程。增加两行如何保证读者的优先权？

事实证明，新的互斥体现在确保了写进程只有在已经有了`sync_mutex`的情况下才会排队等待`writer_mutex`。换句话说，一次只能有一个作家在等待`writer_mutex`。因此，如果一个读者正在等待`writer_mutex`，那么一个作者就没有办法将控制权传递给另一个作者。

#### 监视器

不幸的是，监视器解决方案远不如信号量解决方案优雅。除了添加一个新的条件变量和一个新的共享变量之外，monitor 解决方案还需要分解它的 writer 过程:

```
procedure begin_read(): 
  if writing: 
    safe_read.wait 
  readers++ 
  safe_read.signal

procedure end_read(): 
  readers-- 
  if readers = 0: 
    safe_write.signal

procedure begin_write(): 
  if writing or readers > 0: 
    safe_write.wait 
  writing = true

procedure end_write(): 
  writing = false 
  if safe_read.queue: 
    safe_read.signal 
  else: 
    safe_write.signal 
```

Enter fullscreen mode Exit fullscreen mode

与前面的 monitor 解决方案不同，这个解决方案更多地依赖于通过队列信号驱动访问。特别地，我们维护两个条件变量:`safe_read`和`safe_write`。如果可以安全阅读，我们会向`safe_read`队列发送信号。同时，如果写是安全的，我们就向`safe_write`队列发信号。

从阅读的角度来看，变化不大。如果有任何积极的写作，读者期待等待。否则，他们阅读。当读者完成阅读时，他们应该减少他们的读者计数。与并发读取器解决方案一样，最后一个读取器负责向下一个写入器发出信号。

从写作的角度来看，很多都变了。除了一个新的共享变量`writing`，我们现在有两个过程，而不是一个:`begin_write`和`end_write`。

`begin_write`过程负责验证写操作是否安全(即没有其他人在读，也没有读者)。如果写作不安全，作者应该等待。否则，他们表明他们在写之前写。

同时，`end_write`程序表示写入已经完成。如果有读者在等待，作者负责给他们发信号(也就是读者优先)。否则，他们会向另一个作者发出信号。

对我来说，虽然更复杂，但这个解决方案似乎比信号量解决方案更直观。特别是，我们在进程之间有直接的交流，这看起来更像我们在日常生活中如何执行这样的任务。

## 相关问题

在本文中，我们重点讨论了两种进程同步机制，信号量和监视器，以及如何使用它们来解决一些版本的读者-作者问题。除了本文涵盖的问题之外，还有一些相关的问题，包括:

*   作家的优先权
*   从最小的任务开始
*   交替的读者和作者
*   限制并发读者

我相信还有更多。出于时间的考虑，我选择不解决这些问题。然而，如果你有兴趣了解更多关于这些主题的内容，我很乐意扩展这个列表。

## 想了解更多？

现在，我已经完成了资格考试需要学习的三个主题。现在，我将继续苦读操作系统。然而，在某些时候，我真的需要解决算法问题。

同时，你可以通过成为会员来帮助我发展这个网站。会员被自动添加到[的邮件列表](https://newsletter.therenegadecoder.com/)，他们可以访问额外的内容，比如[的博客文章](https://therenegadecoder.com/category/blog/)。

同样，也欢迎你在这个网站上浏览一些我最喜欢的文章:

*   [语句和表达式的区别](https://dev.to/renegadecoder94/the-difference-between-statements-and-expressions-1e47)
*   [如何教授计算机科学中的数组](https://dev.to/renegadecoder94/how-to-teach-arrays-in-computer-science-2f5n)
*   [与富有挑战性的大学生一起工作](https://therenegadecoder.com/blog/working-with-challenging-college-students/)

一如既往，谢谢你的来访！

读者-作者问题的解决方案第一次出现在叛逆的程序员 T2 的博客上。