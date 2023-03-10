# 2 行代码和 3 个 c++17 特性——霸王模式

> 原文：<https://dev.to/fenbf/2-lines-of-code-and-3-c-17-features-the-overload-pattern-pgg>

[![the overload patter C++17](img/da6aee75267f41aa9152838ef98c7241.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7tDUCI9d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-vXolWEbJW7Q/XFbODfZzQ3I/AAAAAAAADpk/fu612OcMGY0VC6Txn6tdvwq3T6yMv40mgCLcBGAs/s1600/2lines3features.png)

当我为我的书和关于 C++17 的博客帖子做研究时，我偶然发现了这种访问`std::variant` :
的模式

```
template<class... Ts> struct overload : Ts... { using Ts::operator()...; };
template<class... Ts> overload(Ts...) -> overload<Ts...>;

std::variant<int, float> intFloat { 0.0f };
std::visit(overload(
         { ... },
         { ... },
    ),
    intFloat;
); 
```

使用上面的模式，您可以为访问提供单独的 lambdas。

这只是两行紧凑的 C++代码，但是它包含了一些有趣的概念。

让我们来看看这个东西是如何工作的，并一个接一个地了解 C++17 的三个新特性。

最初发布于[bfilipek.com](https://www.bfilipek.com/2019/02/2lines3featuresoverload.html)

## [T1】简介](#intro)

上面提到的代码形成了一种称为`overload`(有时也称为`overloaded`)的模式，它主要用于`std::variant`访问。

使用这样的帮助器代码，您可以编写:

```
std::variant<int, float, std::string> intFloatString { "Hello" };
std::visit(overload
  {
     { std::cout << "int: " << i; },
     { std::cout << "float: " << f; },
     { std::cout << "string: " << s; }
  },
  intFloatString
); 
```

输出:

```
string: Hello 
```

> 小提醒:`std::variant`是辅助词汇类型，有区别的联合。作为一个所谓的 sum-type，它可以在运行时保存不相关的类型，并可以通过重新分配在它们之间切换`std::visit`允许你从给定的变量中调用当前活动类型的操作。在我的博客[中阅读更多关于 C++17](https://www.bfilipek.com/2018/06/variant.html) 中 std::variant 的信息。

如果没有重载，你必须为`()`操作符
编写一个单独的带有三个重载的`class`或`struct`

```
struct PrintVisitor
{
    void operator()(int& i) const {
        std::cout << "int: " << i;
    }

    void operator()(float& f) const {
        std::cout << "float: " << f;
    }

    void operator()(std::string& s) const {
    std::cout << "string: " << s;
    }
};

std::variant<int, float, std::string> intFloatString { "Hello" };

std::visit(PrintVisitor(), intFloatString); 
```

您可能已经知道，编译器在概念上将 lambda 表达式扩展为一个具有唯一名称的类型，该类型具有`operator()`。

我们在重载模式中做的是创建一个从几个 lambdas 继承的对象，然后为`std::visit`公开它们的`operator()`。这样就可以“就地”编写重载。

组成模式的 C++17 特性是什么？

*   在`using`声明中打包扩展——使用可变模板的简洁语法。
*   自定义模板参数演绎规则——允许将 lambdas 的列表转换成`overloaded`类的基类列表。
*   对聚合初始化的扩展——重载模式使用一个构造函数来初始化，但是我们不需要在类中指定它。在 C++17 之前这是不可能的。

## 新增功能

让我们一节一节地探索组成重载模式的新元素。这样我们可以学到一些关于语言的有趣的东西。

### 使用声明

这里有三个特征，但是很难说哪一个最容易解释。

不过还是从`using`说起吧。我们到底为什么需要它？

为了理解这一点，让我们编写一个从两个基类派生的简单类型:

```
#include <iostream> 
struct BaseInt
{
    void Func(int) { std::cout << "BaseInt...\n"; }
};

struct BaseDouble
{
    void Func(double) { std::cout << "BaseDouble...\n"; }
};

struct Derived : public BaseInt, BaseDouble
{
    //using BaseInt::Func;
    //using BaseDouble::Func;
};

int main()
{
    Derived d;
    d.Func(10.0);
} 
```

我们有两个实现`Func`的基类。我们希望从派生的对象中调用该方法。

代码会编译吗？

在进行重载决策集时，C++声明最佳可行函数必须在相同的范围内。

因此 GCC 报告了以下错误:

```
error: request for member 'Func' is ambiguous 
```

点击此处查看演示@Coliru

这就是为什么我们必须将函数纳入派生类的范围。

我们已经解决了一部分，而且不是 C++17 的特性。但是可变语法呢？

这里的问题是之前的 C++17 `using...`不被支持。

在论文[Pack expansions in using-declarations p 0195 r 2](https://wg21.link/P0195)中，有一个很有启发性的例子，展示了减少这种限制需要多少额外的代码:

```
template <typename T, typename... Ts>
struct Overloader : T, Overloader<Ts...> {
    using T::operator();
    using Overloader<Ts...>::operator();
    // […]
};

template <typename T> struct Overloader<T> : T {
    using T::operator();
}; 
```

在上面的例子中，在 C++14 中，我们必须创建一个递归模板定义才能使用`using`。但是现在我们可以写:

```
template <typename... Ts>
struct Overloader : Ts... {
    using Ts::operator()...;
    // […]
}; 
```

现在简单多了！

好吧，但是剩下的代码呢？

### 自定义模板参数演绎规则

我们从 lambdas 派生，然后公开它们的`operator()`,正如我们在上一节中看到的。但是我们如何创建这种`overload`类型的对象呢？

你知道 lambda 的类型是未知的，所以如果没有类的模板演绎，就很难得到它。你会在声明中陈述什么？

```
overload<LambdaType1, LambdaType2> myOverload { ... } // ???
// what is LambdaType1 and LambdaType2 ?? 
```

唯一可行的方法是使用某个`make`函数(因为模板演绎总是适用于函数模板):

```
template <typename... T>
constexpr auto make_overloader(T&&... t) {
   return Overloader<T...>{std::forward<T>(t)...};
} 
```

使用 C++17 中添加的模板推导规则，我们可以简化常见模板类型的创建。

例如:

```
std::pair strDouble { std::string{"Hello"}, 10.0 };
// strDouble is std::pair<std::string, double> 
```

还有一个选项来定义自定义扣除指南。标准库使用了很多，例如`std::array` :

```
template <class T, class... U>  
array(T, U...) -> array<T, 1 + sizeof...(U)>; 
```

而上面的规则允许我们写:

```
array test{1, 2, 3, 4, 5};
// test is std::array<int, 5> 
```

对于霸王夜雨我们可以写:

```
template<class... Ts> overload(Ts...) -> overload<Ts...>; 
```

现在，我们可以输入

```
overload myOverload {  { },  { } }; 
```

并且将正确推导出`overload`的模板参数。

现在让我们来看看这个难题最后缺失的部分——聚合初始化。

### 聚合初始化扩展

这个功能相对简单:我们现在可以初始化一个从其他类型派生的类型。

提醒一下:来自 [dcl.init.aggr](https://timsong-cpp.github.io/cppwp/n4659/dcl.init.aggr) :

> 聚合是一个数组或类，其中
> 
> 没有用户提供的、显式的或继承的构造函数(【class . ctor】)
> 
> 没有私有或受保护的非静态数据成员(【class . access】)
> 
> 没有虚函数，
> 
> 没有虚拟、私有或受保护的基类(【class.mi】)。

例如(规范草案样本):

```
struct base1 { int b1, b2 = 42; };
struct base2 {
  base2() {
    b3 = 42;
  }
  int b3;
};

struct derived : base1, base2 {
  int d;
};

derived d1{ {1, 2}, {}, 4};
derived d2{ { }, {}, 4}; 
```

用`1`初始化`d1.b1`，用`2`初始化`d1.b2`，用`42`初始化`d1.b3`，用`4`初始化`d1.d`，用`0`初始化`d2.b1`，用`42`初始化`d2.b2`，用`42`初始化`d2.b3`，用`4`初始化`d2.d`。

在我们的例子中，它有更重要的影响。

对于我们的重载类，我们可以实现下面的构造函数。

```
struct overload : Fs... 
{
  template <class ...Ts>
  overload(Ts&& ...ts) : Fs{std::forward<Ts>(ts)}...
  {} 

  // ...
} 
```

但是这里我们有很多代码要写，而且可能没有涵盖所有的情况…

使用聚合初始化，我们从基类列表中“直接”调用 lambda 的构造函数，所以不需要编写它并显式地向它传递参数。

## 游乐场

玩代码 [@Coliru](http://coliru.stacked-crooked.com/a/ba1b58473874d45c)

## 总结

过载模式是一件有趣的事情。它演示了几种 C++技术，将它们收集在一起，并允许我们编写更短的语法。

在 C++14 中，您可以从 lambdas 派生并构建类似的助手类型，但只有在 C++17 中，您才能显著减少样板代码并限制潜在的错误。

你可以在关于`overload` [P0051](https://wg21.link/P0051) 的提议中阅读更多内容(不确定这是否会出现在 C++20 中，但值得看看其背后的讨论和概念)。

这篇博文中展示的模式只支持 lambdas，并且没有处理常规函数指针的选项。在本文中，您可以看到一个更高级的实现，它试图处理所有情况。

### 轮到你了

*   你用过`std::variant`和探视机制吗？
*   你用过`overload`模式吗？

### 参考文献

*   [你需要知道的关于 C++17 的 std::variant 的一切](https://www.bfilipek.com/2018/06/variant.html)
*   [如何在多个变量中使用 STD::visit](https://www.bfilipek.com/2018/09/visit-variants.html)
*   [C++周刊- Ep 49 -为什么要继承 Lambdas？](https://www.youtube.com/watch?v=3wm5QzdddYc)
*   [C++周刊- Ep 48 - C++17 的变量`using`](https://www.youtube.com/watch?v=1gNzhE-Tn40)
*   [C++周刊- Ep 40 -从 Lambdas 继承](https://www.youtube.com/watch?v=W-xTpqj31mI)
*   过载:动态构建一个变体访问者——简化 C++！

## 更多来自作者

Bartek 最近出版了一本书-[《C++ 17 详解》](https://leanpub.com/cpp17indetail?utm_source=devto&utm_campaign=2lines3features)可从@Leanpub 获得-与其阅读论文和 c++规范草案，不如用这本书以高效实用的方式学习新标准。