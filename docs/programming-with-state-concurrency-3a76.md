# 用状态编程:并发

> 原文：<https://dev.to/vorahsa/programming-with-state-concurrency-3a76>

在顺序执行模型中，用可变状态编程已经很困难了，在顺序执行模型中，一次只有程序的一部分在执行，函数在不被中断的情况下完成执行。但是，我们的应用程序的上下文并不那么适应，需要程序响应各种异步事件。虽然有些语言，如 Javascript，只采用异步，但许多语言也引入了并发，程序的许多部分可能(至少在概念上)同时执行。

线程是一种常见的并发抽象。由于每个线程都可以访问同一个程序内存，可变状态的问题变得更加严重。现在，状态不仅可以在函数调用之间改变，还可以在函数执行时被另一个线程改变。这个问题通常是通过使用锁来防止多个线程同时在同一状态上操作来解决的，但是锁可能很难正确使用。

## 不变量

从本系列的第一篇文章[来看价值实体模型，多线程还有其他问题。我提到不变量是保持实体状态可管理的有用工具。但是，由于在实体修改自身时，不变量可能会被暂时违反，因此状态查询的并发执行可能会观察到不一致的状态。这又回到了可变状态的所有问题，我们试图通过采用值-实体模型来避免这些问题。](https://dev.to/vorahsa/programming-with-state-values-and-entities-1hcp)

实体内部的锁当然可以用来确保其他线程不会看到临时的不变违例。实体模型中常见问题的另一个问题是，我们通常希望允许对一个实体进行不冲突的并发调用，而设计锁来支持这一点可能会变得复杂。此外，基于锁的系统只支持实体实现者想到的那些操作。例如，关联数组需要的一个常见操作是 insert-if-not-present，但是如果数组只实现检查存在和无条件插入的操作，那么调用者就不能安全地执行这个组合操作。

[软件事务内存(STM)](https://channel9.msdn.com/Shows/Going+Deep/Programming-in-the-Age-of-Concurrency-Software-Transactional-Memory) 是一个有趣的概念，它解决了基于锁的实体风格编程中的许多问题。乐观执行使得非冲突操作成功。将原子操作包装成更大的原子操作的能力解决了操作的可组合性。最后，当用发出修改事件的可观察状态模型编程时，STM 防止在所有状态修改完成之前发出这样的事件。然而，STM 并不是很主流，所以即使许多编程语言都有库，也很难证明采用它是正确的。

## 实体和消息

从“实体”这个词，人们可能会期待一种不同的执行。一个实体让人想起一些独立自主的东西，但这不是线程化执行看待事物的方式。没有强制的实体边界。任何线程都可以随时来执行实体的代码。在单线程系统中架构良好的封装在多线程系统中完全消失了。

我的首选系统遵循这种思想，让每个实体“生活”在一个线程中。属于一个实体的所有代码总是在该实体的线程上执行。这类似于许多需要 UI 组件操作在 UI 线程中发生的 UI 框架，除了我更喜欢系统来处理线程切换，而不是必须手动完成。这样的系统将使编程更加异步，因为每个实体调用都可能涉及到一个线程切换，但是我已经以这种风格编写了代码，并且使用例如[async/await 机制](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/)并不太困难。

这种将对象视为相互通信的独立实体的观点并不新鲜。它类似于艾伦·凯对面向对象编程的定义，面向对象编程专注于对象之间的消息。从并发的角度来看，Erlang 中的[流程在语义上作为具有内部状态的独立实体运行。对我来说，在消息传递模型中更难概念化可观察的实体状态，尤其是第一篇](http://ulf.wiger.net/weblog/2008/02/06/what-is-erlang-style-concurrency/)中描述的派生状态的[依赖网络。不过，当涉及到并发性时，我发现将实体调用视为消息传递是理解系统的一种更好的方式，有助于系统正确工作。](https://dev.to/vorahsa/programming-with-state-values-and-entities-1hcp)

## 减灾实践

有一些模式和思维方式可以缓解可变状态的并发性问题。不像在单线程的情况下，这些往往更难设置，以便它们可以自动工作。因此，需要更多的规则和对所用模式的仔细记录，因为编程环境不能总是强制我们这样做。

避免其他线程观察临时中断的不变量的一种方法是推迟内部状态修改。不是在函数执行期间直接修改实体的状态，而是使用局部变量来捕获所有修改的状态片段，并且仅在执行结束时设置实体的状态。当调用依赖于可能失败的操作时，这也很有帮助，因为还没有进行实际的修改，所以可以简单地放弃执行，而不必实现复杂的回滚逻辑。

当要更新的状态是某种形式的集合(如关联数组)时，这种状态更新方式变得很有趣。完全复制一个大结构进行局部修改可能效率太低(但也可能不是；记得在假设瓶颈在哪里之前进行测量)。一个好的选择是使用一个[持久数据结构](https://en.wikipedia.org/wiki/Persistent_data_structure)，它允许以一种自然的方式在本地进行修改，最后用更新的数据替换原来的数据，但是持久数据结构在许多编程语言中并不常用。这种风格的最终表现是将一个实体的所有(可变)状态保存在一个持久的数据结构中，这将消除任何破坏不变量的问题，因为状态将只在一个操作中一次被修改。

当一个实体变得更复杂时，将它的内部构造成一个[状态机](https://skorks.com/2011/09/why-developers-never-use-state-machines/)会很有帮助，用状态和允许的转换来定义哪些调用是允许的。这在并发和异步情况下也有帮助。也就是说，当状态机实现是线程安全的时，对允许的状态转换的检查可以确保不执行冲突的操作，即使当并发请求时也是如此。

令人惊讶的是，我发现自动化测试很有帮助。一般来说，并发性问题是不确定的，取决于特定的时间条件，这些条件不会完全重现。在适用的情况下，我所做的是在自动化测试中运行大量的线程，所有这些都是在应该是线程安全的实体上进行的，并验证结果是否与一些正确的执行一致。这并不总是可能做到的，通常预期的结果不像传统测试那样清晰，并且测试也不总是能发现失败。但是如果可以定义预期，我发现运行足够多的线程可以足够可靠地解决许多问题。

## 总结

在并发程序中处理可变状态会变得非常困难。并发性使得架构程序变得更加重要，这样状态变化只会以特定的、受控的方式发生。像流行的编程平台经常提供的那样，直接在低级并发抽象上构建程序通常会带来麻烦。只要有可能，尽量将更高层次的概念提取到可重用的组件中，比如[任务执行器](https://winterbe.com/posts/2015/04/07/java8-concurrency-tutorial-thread-executor-examples/)或者[操作队列](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationObjects/OperationObjects.html)。更严格地控制状态如何变异。当不可能通过语言强制机制确保正确操作时，要非常自律，并仔细记录正在做的事情和原因。