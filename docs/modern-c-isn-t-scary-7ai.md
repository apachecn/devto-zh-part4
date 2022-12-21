# 现代 C++并不可怕

> 原文：<https://dev.to/jdsteinhauser/modern-c-isn-t-scary-7ai>

C++被认为是一种迟钝、过时的语言，只适合那些戴衣领的人和愤怒的教授。当然，它很快，但是为什么你要为了更快一点而学习所有那些讨厌的开销呢？我爱我的包管理器和工具！C++有什么现代的东西吗？

这正是我大学毕业后每次看 C++或做 C++项目时的感受。然而，最近我不得不写了大量的 C++，我不得不说...

C++17 不是你爸爸的 C++。

# 不那么可怕的语法

C++因其可怕的语法而臭名昭著。然而，从 C++11 开始，语法逐渐变得更有吸引力。很多都涉及到关键字`auto`的使用。你现在可以像在 Python 和 C#中一样使用基于范围的`for`循环(技术上是`foreach` ):

```
double arr[3] = { 1.0, 2.0, 3.0 };

for (auto& x : arr) {
    std::cout << x << std::endl;
} 
```

Enter fullscreen mode Exit fullscreen mode

`auto`后面的`&`表示我们不想复制数组中的项目(或`std::vector`或任何其他可迭代的集合)。
Auto 可以用来析构`std::tuple` s 和`std::pair` s，以及`struct` s 和数组！如果我们使用上面的数组，我们可以将整个东西析构:

```
auto [a, b, c] = arr;
// auto [a, b] won't compile because our array is length 3 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在`std::map` s 上的`for`循环中使用它:

```
for(const auto& [k,v] : dictionary) {
...
} 
```

Enter fullscreen mode Exit fullscreen mode

析构元组的工作方式相同:

```
std::tuple<double, std::string> t{ 4.0, "GPA" };
auto [value, label] = t; 
```

Enter fullscreen mode Exit fullscreen mode

这个元组看起来是用标准的、冗长的 C++定义的。嗯，他们也有解决办法...

# 模板参数推断

如果可以在模板类型中推断出类型，那么就没有必要指定类型！我可以将上面的`std::tuple`定义改写为:

```
std::tuple t{ 4.0, R"(GPA)" };
// or better yet
auto t = std::tuple{ 4.0, R"(GPA") }; 
```

Enter fullscreen mode Exit fullscreen mode

`R"(...)"`创建一个字符串而不是一个字符数组。这比 C++语言标准的早期版本要小得多，并且使类型签名不那么烦人。你仍然需要在函数返回类型上完全限定类型，但是你可以`auto`在其他地方消除这种痛苦。

语言标准委员会还增加了`std::tie`来与现有的代码集成，在这里你可以将作为函数输出的值绑定到已经存在的变量上。对于有多个输出值的函数来说，这种语言确实使它比过去好得多(T2 ),我对此非常满意。

# 文字量

C++很早就有了一些整数和浮点类型的文字(例如，将零定义为无符号长整型(如`0UL`)或浮点型(如`0.0f`)。在 C++11/14 中，已经扩展了文字的范围，包括布尔值、字符串(UTF-8 *和* UTF-16)、时间单位(`42ns`与`std::chrono::nanoseconds(42)`相同)等等！还有一个选项是[用户定义的文字](https://en.cppreference.com/w/cpp/language/user_literal)，文档也非常可靠。对我个人来说，这是最令人兴奋的特性之一！

# 多平台独立库

在 C++中，有几样东西是典型的特定于操作系统的；没有总体的抽象概念。幸运的是，在 C++11 和更高版本中，这个问题已经得到了解决。

例如，如果你想让当前线程休眠 5 秒钟，在爷爷的 C++中你应该写:

```
#include <unistd.h> 
...
void sleep_func(unsigned int milliseconds) {
    usleep(milliseconds * 1000);
}
...

unsigned int sleep_seconds = 5;
sleep_func(sleep_seconds * 1000); 
```

Enter fullscreen mode Exit fullscreen mode

这将适用于 nix 环境【仅 T2】。如果您想让睡眠的单位不是微秒(Unix 实现的度量单位)，那么您必须自己进行转换。虽然不难，但仍然容易出现人为错误。如果你想在一个特定的线程上做...嗯，那是完全不同的故事。在 C++11 中，你可以使用`std::chrono`库和[时钟](https://en.cppreference.com/w/cpp/header/chrono)，以及用于线程特定任务的`std::thread`库。

```
#include <chrono>
#include <thread> 
...
void sleep_func(std::chrono::duration duration) {
    std::this_thread::sleep_for(duration);
}
...

auto sleep_seconds = 5;
sleep_func(std::chrono::seconds(sleep_seconds)); 
```

Enter fullscreen mode Exit fullscreen mode

在`std::chrono`名称空间中也有几个预定义的持续时间，因此可以非常清楚地看到编译器是如何处理时间跨度和所有转换的。少给我们干活！

他们还**最终**在 C++17 中实现了一个[文件系统](https://en.cppreference.com/w/cpp/filesystem/path)抽象！它在 C++14 中是实验性的，但在最新版本中正式成为语言标准的一部分。

# 在模板类中创建对象

在 C++的“好日子”里，使用模板集合是非常烦人的。例如，如果您想将某个对象推到队列的后面，您必须创建该对象，然后将该对象的副本传递到队列中。

```
std::vector<thing> things;

for (int i = 0; i < 50; i++) {
   thing t("foo", i);
   things.emplace_back(t);
} 
```

Enter fullscreen mode Exit fullscreen mode

许多模板类现在都有函数，这些函数会复制以前有`Args&&`风格实现的函数，这样就可以在模板类中创建该类型的新对象！那看起来像:

```
std::vector<thing> things;

for (int i = 0; i < 50; i++) {
    things.emplace_back("foo", i);
} 
```

Enter fullscreen mode Exit fullscreen mode

这节省了一些复制开销，也加快了速度，并且不鼓励在集合中使用指针(在适当的时候)。

# 更好的指针

让我们面对现实:处理原始指针很糟糕。分配内存。释放内存。在一个作用域中创建一个指针，并假设所有权随后转移到一个新的作用域。所有这些情况都需要大量的仪式，可能会导致内存泄漏，并且需要大量的精神开销来确保不会导致内存泄漏。令人欣慰的是，C++11 将 Boost 库中开发的不同指针类型引入了语言规范。

## 唯一指针

`std::unique_ptr<T>`只能被一个对象引用。如果需要将所有权转移到不同的作用域(但仍然只维护一个副本)，可以使用 std::move 来转移所有权。这在工厂或任何其他您可能想要创建一些东西并将其所有权传递给另一个对象的时候非常有用。例如，您可能想要创建一个来自`Socket`的字节流，并将该数据的所有权传递给一个请求对象。

## 共享指针

是我在 C++中最喜欢的东西之一。如果某个点需要被多个对象引用(比如说，一个 Websocket 的单个对象)，在老式的 C++中，你会创建一个指向该对象的原始指针，并在清理时或最后一次使用后销毁它...*希望是*。原始指针是 C++中导致内存泄漏的最大因素之一。他们可能会把空指针列为十亿美元的错误。

幸运的是，共享指针现在已经成为一种东西，并在 C++社区中被广泛接受。当被复制时(它们应该**总是**被复制，而不是通过引用或指针传递)，它们增加一个内部引用计数。当这些副本被销毁时，引用计数递减。当引用计数达到零时，对象被销毁，内存被释放。不再有手动内存管理的麻烦！耶！如果你不严格复制共享指针，你仍然可以用它们来避免失败，但是现在有一个比共享的原始指针更好的安全机制。

# 结论

尽管感觉上 C++的语法仍然比需要的多，但它并没有我记忆中的那么糟糕。与过去的工作相比，我更喜欢在过去的 4 个月里每天都在开发它。如果你再次检查它，希望它也不会让你感到害怕！

编码快乐！