# C#泛型集合

> 原文：<https://dev.to/scottslatton/c-generic-collections-1g3g>

我以前听过的一句话是:“Javascript 没有数组，它有列表”，在知道它的意思之前，我肯定会重复这句话。我一直认为这基本上没有任何意义，因为“功能上，它们是相同的”，当你第一次学习 Javascript 时，这可能是一个很好的哲学，因为你不会陷入理论的泥潭，可以将注意力集中在构建东西上。然而，当我开始钻研 C#时，开始在日常生活中使用它，这种哲学很快就跟上了我。我很早就知道，Javascript 以外的语言中的数组有一个固定的长度，你必须在某个时候声明它。这是令人难以置信的恐吓，我很难理解你是如何绕过这个限制的。我想和你分享我的“啊哈！”那一刻，我终于明白，学习数据结构以及何时使用它们对于创建适当的、实用的和优化的代码变得如此重要。

C#在中有一个命名空间。NET 库称为[系统。在这里你可以找到你的列表、栈和队列。这些通常是您必须在 Javascript 中手动构建的数据结构。您可以像普通的对象声明一样实例化其中的任何一个。](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic?view=netframework-4.8)

```
List<T> grades = new List<T>(); 
```

旁注:如果这是你第一次看到，它是一个[通用类型参数](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generic-type-parameters)，意味着你可以用编译器识别的任何类型填充这个参数，比如 string 或 double。

列表显然比普通数组有更高的内存开销，但是它解决了我们的问题，如果你不确定的话，我们需要知道数组中有多少项。关于使用这些内置的数据结构，我最喜欢的一点是它们还带有内置的方法。您可以通过调用。列表对象实例上的 BinarySearch()。