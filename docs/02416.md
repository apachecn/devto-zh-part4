# 移动语义解释

> 原文：<https://dev.to/benw/move-semantics-explained-3dj7>

在`C++11`中，move 语义和右值引用被添加到语言中。有了它们，我们可以通过避免不必要的临时对象副本来显著提高性能。

## 左值和右值引用

首先要理解*左值*和*右值*的概念。简单来说，*左值*表达式可能出现在赋值的右边或左边，而*右值*可能只出现在右边。这是一个过于简化的解释，在现代 C++中，它比这更微妙，但是为了我们的目的，我们可以看一些例子来使它更清楚:

```
int* int_ptr = nullptr;
// Example 1.
int i = 0;
int_ptr = &i;
/* Ok! because i is an lvalue,
for which the address can be referenced.*/
*int_ptr = 5;

// Example 2.
int func();
/* ERROR! that's an rvalue. We can't reference
the address of the temporary created on that call. */
func() = 5;

// Example 3.
int& func();
func() = 7;// OK! Like operator[] in std::vector for example.

//Example 4.
int a = 3, b = 4; // both lvalues
(a+b) = b; //ERROR 
```

正如我们从上面的例子中看到的，一个*右值*是一个表达式，它的生存期结束于它被求值的表达式。在该上下文中，它可以被认为是一个未命名的临时对象。

## 移动语义

假设我们有一个类，它拥有一个构建或复制成本很高的资源，例如一个大的内存缓冲区。

```
class Expensive {
  std::string name_;
  size_t big_arr_size_ = 0;
  uint8_t *big_array_ = nullptr;

 public:
  Expensive(const std::string &name, size_t arr_size)
      : name_(name),
        big_arr_size_(arr_size),
        big_array_(big_arr_size_ ? new uint8_t[arr_size] : nullptr) {
  }

  ~Expensive() {
    delete[] big_array_;
  }
}; 
```

复制构造函数和复制赋值操作符:

```
 Expensive(const Expensive &other)
      : name_(other.name_),
        big_arr_size_(other.big_arr_size_),
        big_array_(big_arr_size_ ? new uint8_t[big_arr_size_] : nullptr) {
    std::copy(other.big_array_, other.big_array_ + big_arr_size_, big_array_);
  }

  // note: Might be a better option, using the copy and swap idiom.
  Expensive &operator=(const Expensive &other) {
    if (&other != this) {
      name_ = other.name_;
      if (other.big_arr_size_ != big_arr_size_) {
        // In case throws, would not want to change the state of the object.
        auto new_size = other.big_arr_size_;
        auto new_arr = new_size ? new uint8_t[new_size] : nullptr;
        // resource needs clean up and reallocation
        delete[] big_array_;
        big_arr_size_ = new_size;
        big_array_ = new_arr;
      }
      std::copy(other.big_array_, other.big_array_ + big_arr_size_, big_array_);

    }
    return *this;
  } 
```

在复制构造或复制赋值期间，我们为构造/赋值的对象分配并赋值给一个(可能很大的)缓冲区。
我们来看下面这个例子:

```
Expensive first("first", 1024);
Expensive second("second", 2048);

// Assignment of two lvalue. Copy assignment is called.
first = second

/* Assignment of a temporary object,
which is destructed right after expression evaluation.
Copy assignment is called (no move operations defined yet),
but we can do better. */
first = Expensive("third", 4096); 
```

目前，第二个作业的操作顺序与第一个作业相同。创建临时对象后，赋值操作符被调用，这又调用缓冲区的分配、删除和复制。对第二种情况进行特殊处理可能会更有效，这里我们处理的是一个*右值*，一个临时对象，它将在赋值后立即被析构。通过这种方式，我们可以将昂贵的赋值替换为更类似于指针交换的东西，这样我们就不需要重新分配数据和复制其内容。这就是 move 语义在使用*右值*引用时发挥作用的地方。在语法上，给定一个类型`T`，`T&`称为*左值*引用，`T&&`是*右值*引用。当我们想要从*右值*引用构造或赋值时，我们称之为移动操作。有移动赋值和移动构造操作，在我们的例子中可能是这样的:

```
 Expensive(Expensive &&other) {
    std::swap(name_, other.name_);
    std::swap(big_arr_size_, other.big_arr_size_);
    std::swap(big_array_, other.big_array_);
  } 
```

在这个实现中，我们交换缓冲区的指针，这是一个比重新分配和复制便宜得多的操作。重要的是让我们移动的对象(在我们的例子中称为 other)处于定义良好的状态，并清除其资源。(创建的对象具有空字符串、空指针和零大小，因此满足要求)。
注意，尽管“other”作为一个*右值*引用被传递，但它本身是一个左值，因为它有一个我们可以引用的地址。如果变量有名字，它就是一个*左值*。函数`std::move`将其参数转换为一个*右值*引用(不区分其参数是一个*左值*还是一个*右值*)，因此它返回一个*右值*。

让我们来看看移动赋值:

```
 Expensive &operator=(Expensive &&other) {
    name_ = std::move(other.name_);
    std::swap(big_arr_size_, other.big_arr_size_);
    std::swap(big_array_, other.big_array_);
    delete[] other.big_array_;
    other.big_array_ = nullptr;
    other.big_arr_size_ = 0;
    return *this;
  } 
```

注意`name_`构件上`std::move`的使用。因为`other.name`是一个*左值*，如果我们写了`name_ = other.name_`，那么`std::string`的副本将被调用。因为我们想利用它是一个移动操作，为了调用`std::string`的移动赋值，我们可以在`other.name`上调用`std::move`，它作为一个右值返回。在赋值时，`std::swap`的实现在它的参数上使用了`std::move`，所以我们可以在`name_`上调用`std::swap`，就像在 move c'tor 中写的那样。

## 编译器生成的特殊成员函数

C++11 之前的特殊成员函数有:默认 c'tor、d'tor、复制 c'tor 和复制赋值操作符。这些成员函数是在需要时生成的(也就是说，它们在代码中被调用)，并没有在类中显式声明。使用 move 语义，编译器可能会生成另外两个成员函数:move c'tor 和 move 赋值操作符。move c'tor move 构造类的每个非静态数据成员，类似地，move 赋值 move-赋值每个非静态数据成员。如果一个数据成员的类型不支持移动操作，那么它将被复制构造/赋值。

当该类显式定义了一个复制副本时，将不会生成移动操作，如果显式定义了移动操作，也不会生成复制操作。背后的逻辑是，如果成员式复制不适合该类，那么成员式移动可能也不适合，反之亦然。三原则规定，如果你声明了拷贝 c'tor、拷贝赋值操作符或析构函数中的任何一个，你应该声明所有三个。这条规则来自于这样的观察:如果类在复制操作中定义了一些特殊的处理，那么它可能会进行一些资源管理，并且这些资源应该在复制操作和析构函数中被处理。这个规则是这样一个事实背后的动机:如果一个 d'tor 被明确定义，那么移动操作就不会被生成。因此，只有在没有定义移动操作，也没有定义定向或复制操作的情况下，编译器才会生成移动操作。
现在让我们看看最后一段的含义。假设我们有一个包含`std::vector`的类，我们知道为它定义了移动操作。

```
struct ClassGenMove {
  ClassGenMove() : bytes_(10'000'000) {}
  std::vector<uint8_t> bytes_;
}; 
```

因此，在这个例子中，类没有定义 d'tor 或 copy 操作，这意味着编译器将生成 copy 操作、move 操作和析构函数。由于编译器生成的移动操作使成员移动，当从*右值*赋值或构造时，移动赋值或移动构造将发生在`bytes_`成员上。
当我们添加一个析构函数时会发生什么:

```
struct ClassNotGenMove {
  ClassNotGenMove() : bytes_(10'000'000) {}
  std::vector<uint8_t> bytes_;
  ~ClassNotGenMove() = default;
}; 
```

如前所述，编译器不会生成移动操作。现在我们可以测试这种变化的性能。

```
template <typename T>
void TimeMoveVsCopy() {
  T sample1, sample2;

  auto num_iter(10000);
  auto start_time = std::chrono::high_resolution_clock::now();
  for (auto i(0); i < num_iter; i++) {
    sample1 = std::move(sample2);
  }
  auto end_time = std::chrono::high_resolution_clock::now();

  std::cout << "Took "
            << std::chrono::duration_cast<std::chrono::microseconds>(end_time -
                                                                     start_time)
                       .count() /
                   num_iter
            << " us" << std::endl;
}
std::cout << "Class with generate move ops:\n";
TimeMoveVsCopy<ClassGenMove>();

std::cout << "Class without generated move ops:\n";
TimeMoveVsCopy<ClassNotGenMove>(); 
```

在 MacBook Pro (3.5 GHz 英特尔酷睿 i7)上测量的性能:

```
Class with generate move ops:
Took 6 us
Class without generated move ops:
Took 745 us 
```

不出所料，没有生成移动操作，因此调用了`std::vector`的复制副本，我们认为这是对性能的影响。遵循前面讨论的关于编译器生成的特殊成员函数的规则，在添加析构函数之后，我们需要显式声明移动和复制操作，在这种情况下，只使用缺省值就足够了。现在我们回到了预期的使用移动操作。

```
ClassNotGenMove &operator=(const ClassNotGenMove &) = default;
ClassNotGenMove(const ClassNotGenMove &) = default;
ClassNotGenMove(ClassNotGenMove &&) = default;
ClassNotGenMove &operator=(ClassNotGenMove &&) = default; 
```

### 参考文献

*   [http://th Becker . net/articles/r value _ references/section _ 01 . html](http://thbecker.net/articles/rvalue_references/section_01.html)
*   [https://Eli . the green place . net/2014/perfect-forwarding-and-universal-references-in-c](https://eli.thegreenplace.net/2014/perfect-forwarding-and-universal-references-in-c)
*   斯科特·迈尔斯的《有效的现代 c++》
*   [https://www.artima.com/cppsource/rvalue.html](https://www.artima.com/cppsource/rvalue.html)
*   [https://stack overflow . com/questions/36232671/rvalue-references-what-exactly-is-temporary-objects-what-is-they-scope-an](https://stackoverflow.com/questions/36232671/rvalue-references-what-exactly-are-temporary-objects-what-is-their-scope-an)
*   [https://shaharm . com/CPP/值/](https://shaharmike.com/cpp/rvo/)