# 多任务和多线程核心概念

> 原文：<https://dev.to/lucasepe/multitasking-and-multithreading-core-concepts-4c77>

# 一心多用

> 多任务是在一定时间内多个任务(也称为进程)的并发执行。

新任务可以在已经开始的任务完成之前打断它们，而不是等待它们结束。结果，计算机以交错的方式执行多个任务的片段，而这些任务共享公共处理资源，例如中央处理单元(CPU)和主存储器。

## 上下文切换

在任何给定的时间，处理器(CPU)都在特定的上下文中执行。

这个上下文由它所寻址的寄存器和内存(包括堆栈、数据和代码)的内容组成。

> 当处理器需要切换到不同的任务时，它必须保存其当前的上下文(以便它可以在以后恢复上下文并从它停止的地方继续执行)并切换到新任务的上下文。

这种“上下文切换”可以以固定的时间间隔启动(抢先式多任务)，或者运行的程序可以被编码为当它可以被中断时向管理软件发出信号(协作式多任务)。

## 合作 vs 抢先(多任务)

| 合作的 | 先发制人的 |
| --- | --- |
| 处理器的使用永远不会从任务中被拿走；相反，在任何其他任务可以运行之前，一个任务必须自愿放弃对处理器的控制 | 操作系统可以在没有任务合作的情况下控制处理器(任务也可以主动放弃，就像在非抢占式多任务处理中一样)。一个任务被夺走控制权的过程叫做抢占 |
| 所有程序必须合作，整个调度方案才能工作 | 操作系统从任务中控制处理器:当任务的时间片用完时；当具有较高优先级的任务准备好运行时 |

# 多线程

> 多线程是多个线程的并发执行。

# 螺纹

> 一个线程只是一系列可以由处理器/任务独立执行的指令。

进程和线程都是独立的执行序列。

典型的区别是(同一个进程的)线程运行在共享内存空间，而进程运行在单独的内存空间。

进程<u>中的所有线程共享</u>相同的内存和系统资源(包括配额限制)。

现代处理器可以一次执行多个线程(多线程)。

在多线程程序中，程序员负责确保不同的线程不会以与另一个线程使用相同资源相冲突的方式使用这些共享资源，从而相互干扰。正如您可能会怀疑的那样，这可能会有点棘手。

## 同步

修改由多个线程共享的数据结构的代码段被称为“关键段”。

重要的是，当临界区在一个线程中运行时，其他线程不能访问该数据结构。

> 同步是必要的，以确保一次只有一个线程可以在临界区执行。

对将在线程间共享数据结构或指令或对象的访问必须同步。

1.  在访问数据结构之前等待同步
2.  访问数据结构——这是关键部分
3.  解锁同步，以便其他线程可以访问数据

第一步非常关键，因为如果忽略了这一步，任何线程都可以在你访问的时候访问数据结构。

最后一步也很关键——如果省略，那么即使在完成之后，也没有线程能够访问数据。

在临界区使用这种技术可以确保一次只有一个线程可以访问数据。

### 参考文献

[https://sqljunkieshare . com/2012/01/06/抢占式与非抢占式多任务与多线程/](https://sqljunkieshare.com/2012/01/06/preemptive-vs-non-preemptive-and-multitasking-vs-multithreading/)

[https://www . geeks forgeeks . org/operating-system-difference-多任务-多线程-多处理/](https://www.geeksforgeeks.org/operating-system-difference-multitasking-multithreading-multiprocessing/)