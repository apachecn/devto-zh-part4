# 让我们用 C++编写标记联合

> 原文：<https://dev.to/aka_dude/let-s-write-tagged-union-in-c-1chf>

*免责声明:生成的代码将远比 std 或 boost“变体”简单。本文更多的是关于理解可变模板，而不是关于编写全功能的产品化类。此外，我还不是专业的程序员，所以下面的信息和代码更接近于互联网研究，而不是讲座。非常欢迎评论。*
**为了理解这篇文章，你需要了解常见的(非可变的)C++模板。**

## 动机

我一直想“获得”可变模板，但是推动我写这段代码的是我的项目。这是一个微控制器，你可以通过 wi-fi 连接到它，并从它的传感器读取信息。问题是，不同的传感器返回不同类型的值。例如，可以有温度传感器，它给你`short unsigned int`，和加速度计，它给你`long int [3]`(三个负值和正值)。因为我想要一个通用的接口来读取和发送这些值，所以我需要一种方法来在单个矢量/地图中存储不同类型的值，等等。，最常用的方法是标记联合。

## 标记的并集

标记联合是一种数据结构，它至少可以做三件事:

1.  存储指定类型之一的实例；
2.  检查存储值是否是某种类型的实例；
3.  获取存储值。

让我们想象一下使用标记联合的代码会是什么样子:

```
// Create it
Variant<short unsigned int, long int [3]> v;
// Store value
v = (long int [3]) {100, -10, 925};
// Check type of stored value:
v.is<long int [3]>() // => true
// Get the value
v.as<long int [3]>() // => {100, -10, 925} 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是可变模板？

可变模板允许您使用任意数量的模板参数。它们的语法看起来类似于 Python 中的`*`列表操作符

```
template<typename... Ts> // Like `def func(*args)`
struct MyClass {
    int field = some_function<Ts...>(); // Like `field = some_f(*args)`
} 
```

Enter fullscreen mode Exit fullscreen mode

python 的“星形折叠”和 variadic 变量(在上面的例子中是它的`Ts`)的巨大区别在于，python 的参数列表实际上是*列表*，所以你可以迭代它或者通过索引获取元素(`lst[i]`)。C++中没有语法来做同样的事情。让我们假设我们有类似于 Python 的 list 的语法。我们将如何书写我们的`Variant`？

```
#include <cstddef>
#include <iostream>
#include <exception> 

template<typename... Ts>
class Variant {
    // index of currently stored type
    size_t curr_type; 
    // we'll store our data here
    char* data; 
public:
    // "Empty" constructor
    Variant():
        curr_type(0),
        data(nullptr) {}

    // Invoked on `v = ...;`
    template<typename T>
    const T& operator=(const T& x) {
        // The following line will be changed later
        this->curr_type = <py> Ts.find(T) </py>;
        if (data)
            delete[] data;
        // Treat `x` as bytes and copy them
        this->data = new char[sizeof(T)];
        auto ptr = reinterpret_cast<const char*>(&x);
        std::copy(ptr, ptr + sizeof(T), this->data);
        return x;
    }

    // Check type of stored value
    template<typename T>
    bool is() {
        return data != nullptr &&
            <py> Ts.find(T) </py> == this->curr_type;
    }

    // Get value (and catch error if given type is wrong)
    template<typename T>
    T& as() {
        if (!this->is<T>())
            throw std::runtime_error("Wrong type");
        return *reinterpret_cast<T*>(this->data);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

好的，现在我们需要一种方法来找到 T 在 Ts 中的索引。为了找到它，我们需要更深入地研究模板。

## 模板化:感受力量

C++允许你用不同的模板参数编写模板，越来越通用(对于类/结构模板是这样，而不是函数式的)。在编译时，编译器采用与给定参数相匹配的最具体的模板。如果由于某种原因无法编译这个模板，那么它会转而使用更通用的模板。听起来很复杂，所以我们来看看代码:

```
// Firstly declare the most generic template
// `A`, first parameter, is type to find. Rest of them are types to search through.
// `value` is result of our search
template<typename A, typename T, typename... Ts>
struct index_type {
    static const size_t value = index_type<A, Ts...>::value + 1;
};

// Then template, matching the situation, when type to be found has index of 0
template<typename A, typename... Ts>
struct index_type<A, A, Ts...> { // attention to `<A, A, Ts...>`
    static const size_t value = 0;
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在类实现中，我们需要用
替换`<py> Ts.find(T) </py>`

```
index_type<T, Ts...>::value 
```

Enter fullscreen mode Exit fullscreen mode

这是如何工作的？我在函数式编程方面没有丰富的经验，但对我来说，这段代码与声明式编程有很多共同之处。

第二个模板声明说“如果要查找的类型与类型列表的头相同，则将`value`设置为 0”。
第一个模板(仅在第二个失败时执行)说“否则，递归地继续搜索列表的尾部”。

最终代码在[要点](https://gist.github.com/Mr-Andersen/cd5f9d8740e03b3a779bd49da281652d)上。与上面的代码相反，它使用`char[]`来存储值，以及一些更可变的模板，但是上面的代码也可以工作。