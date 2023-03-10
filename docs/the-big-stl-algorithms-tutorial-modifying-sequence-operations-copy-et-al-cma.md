# 大 STL 算法教程:修改序列操作-移动和交换

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-modifying-sequence-operations-copy-et-al-cma>

在[大 STL 算法教程](http://sandordargo.com/blog/2019/01/30/stl-algos-intro)的下一部分，我们将发现一些涉及移动或交换的修改序列操作:

*   `move`
*   `move_backward`
*   `swap`
*   `swap_ranges`
*   `iter_swap`

## `move`

`move`与 [`copy`](http://sandordargo.com/blog/2019/08/14/stl-alogorithms-tutorial-part-5-copy-operations) 非常相似，它们都用两个迭代器定义输入范围，一个迭代器标记输出范围的开始。

当`copy`保持输入不变时，`move`将*将*对象从一个范围转移到另一个范围。它使用了 C++11 eleven 中引入的 move 语义，意味着算法本身从 C++11 开始就可用了。

对源对象的处理通常在其移动赋值操作符中定义。但是请注意，如果没有实现移动赋值操作符，那么在对象上调用`std::move`不会失败。你甚至不会得到一个编译器警告。相反，将调用可用的赋值运算符。

`std::move`的用法是一种可能性，不是你想当然的。重复一下，这意味着如果编译器没有找到移动构造函数/移动赋值操作符的实现，那么它将简单地使用复制构造函数/赋值操作符。

使用您的类型，您可以控制它，但是在一个大的旧代码库中，您可能看不到或者忘记检查是否支持移动语义，您认为您可以使用它们，而事实上，您不支持。这可能会让您付出一些您不想使用的性能开销。

这里有一个如何使用它的示例。

```
#include <algorithm> #include <iostream> #include <vector> #include <string>  class A {
public:  
A(const std::string& a) : m_a(a) {
  // std::cout << "this is not a move but simple ctr\n"; 
}  

A(const A& a) : A(a.m_a) {
  // std::cout << "this is not a move but copy ctr\n";
}   

A& operator=(const A& other) {    
  this->m_a = other.m_a;
  std::cout << "this is not a move but an assignment operator\n";
  return *this;
}   

A& operator=(A&& other) {    
  this->m_a = std::move(other.m_a);
  std::cout << "this is now move assignment\n";
  return *this;
}

std::string toString() const {
  return m_a;
}

private:
  std::string m_a;
};

int main() {  

  auto myVect = std::vector<A>{A("1"), A("2"), A("3"), A("4"), A("5")}; 
  auto outputVect = std::vector<A>{5, std::string("0")};
  outputVect.reserve(myVect.size());
  std::cout << "The content of myVect: ";
  for (const auto& a : myVect) {
    std::cout << a.toString() << " ";
  }  

  std::cout << "\n";
  std::cout << "The content of outputVect: ";
  for (const auto& a : outputVect) {
     std::cout << a.toString() << " ";
  }
  std::cout << "\n";

  std::cout << "LET'S MOVE\n";
  std::move(myVect.begin(), myVect.end(), outputVect.begin());
  std::cout << "MOVES are done\n";

  std::cout << "The content of myVect: ";
  for (const auto& a : myVect) {    
    std::cout << a.toString() << " ";
   }  
  std::cout << "\n";
  std::cout << "The content of outputVect: ";
  for (const auto& a : outputVect) {
    std:: cout << a.toString() << " ";
  }  
  std::cout << "\n";
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们对 [`copy`](http://sandordargo.com/blog/2019/08/14/stl-alogorithms-tutorial-part-5-copy-operations) 所讨论的，输出范围要么必须为要移入的对象提供足够的空间，要么也可以使用插入操作符。顾名思义，它将帮助您向输出向量添加新元素。可以这样用:

```
std::move(myVect.begin(), myVect.end(), std::back_inserter(outputVect)); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，当您创建输出向量和/或为它保留足够大的空间时，您可以简单地使用默认的构造函数。

您可能会想到的一个特殊问题是，我们的输出容器在开始时是空的，它会不断增长。有几个步骤？我们无法提前知道你正在使用的编译器的实现细节。但是，如果您的输入容器足够大，您可以假设输出操作符将在多个步骤中增长。调整向量的大小可能很昂贵，它需要内存分配，寻找连续的空闲区域，等等。

如果您想在这方面有所帮助，您可以使用`std::vector::reserve`，它将为 vector 保留足够大的内存区域，这样它就可以在没有新分配的情况下增长。如果预留的大小不够，就不会出现分段错误或其他任何问题，只是一个新的分配。

我们可以观察到的是，`std::move`就像`std::copy`一样，不会自己插入新元素，但是会覆盖输出容器中的现有元素。它只能在使用插入器迭代器时插入。

## `move_backward`

`move_backward`类似于`copy_backward`。该算法从输入范围移动元素，但从后面开始向开始移动。

与输入相比，它是否产生了相反的顺序？不，不是的。它维持秩序。那么这个`move_backward`为什么会存在呢？它有什么用？答案和示例与`copy_backward`的答案和示例非常相似。

我们来思考下面这个案例。

我们有一个输入范围`{1, 2, 3, 4, 5, 6, 7}`，我们想将部件`{1, 2, 3}`移动到`{2, 3, 4}`上。为了更直观:

```
{1, 2, 3, 4, 5, 6, 7} => { , 1, 2, 3, 5, 6, 7} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们尝试使用`std::move`并且输出容器与输入相同。

你可以试试这个代码:

```
#include <iostream> #include <algorithm> #include <vector>  int main () { 
 auto inputNumbers = std::vector<std::string>{"1", "2","3","4","5","6","7"};
 std::move(std::begin(inputNumbers), std::begin(inputNumbers)+3, std::begin(inputNumbers)+1);
 for (auto number : inputNumbers) {  
  std::cout << number << "\n";
 } 
 return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

输出可能与您预期的不同——这取决于您的预期和编译器:

```
 1
5
6
7 
```

Enter fullscreen mode Exit fullscreen mode

发生了什么事？

首先，将第一个数字(`inputNumbers.begin()`)移动到第二个数字(inputNumbers.begin()+1)上。所以 2 被 1 覆盖，原来的 1 现在被清除。然后第二个数字(`inputNumbers.begin()+1`)移动到第三个(`inputNumbers.begin()+2`)位置。但是现在，第二个数字是 1，所以它将被移到第三个数字中。诸如此类。

*(有可能你正在使用一个足够聪明的编译器来克服这个问题)*

`std::move_backward`会帮助你不要有这个问题。首先，它将移动输入范围的最后一个元素，然后一个接一个地向第一个元素移动，保持输出中的相对顺序。当向右移动且输入范围与输出范围重叠时，使用`move_backward`。请记住，当您使用`std::move`作为输出时，您添加了第一个输出位置(从容器的开始)，使用`std::move`您必须传递最后一个位置。

```
#include <iostream> #include <algorithm> #include <vector>  int main () { 
  auto inputNumbers = std::vector<std::string>{"1", "2","3","4","5","6","7"};
  std::move_backward(std::begin(inputNumbers), std::begin(inputNumbers)+3, std::begin(inputNumbers)+4);
  for (auto number : inputNumbers) {  
    std::cout << number << "\n";
  } 
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `swap`

并没有给我们带来多少惊喜。就是交换两个传入变量的内容。它们可以是内置类型、容器、用户定义的对象。

在 C++11 之前，它使用复制构造函数来创建临时对象，使用复制赋值操作符来执行赋值。

从 C++11 开始，它利用了可用的移动语义。

下面是一个非常简单的用法示例:

```
#include <iostream> #include <algorithm> #include <vector>  int main () { int x=42, y=51;
 std::cout << "Before swap x: " << x << ", y: " << y << "\n";
 std::swap(x,y);
 std::cout << "Before swap x: " << x << ", y: " << y << "\n";
 return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `swap_ranges`

`swap_ranges`以三个迭代器为参数。前两个定义了要交换的一个范围，而要交换的另一个范围仅由其起点来表征。这是有意义的，因为两个范围应该具有相同的长度。

我写的是应该，不是必须。

如果没有东西可以交换，就没有错误，也没有警告。我们将丢失从第一个范围换出的内容，取而代之的是，我们将得到一个默认的构造对象。

这意味着如果使用不当，你会很危险。

这里有一个你可以尝试的例子:

```
#include <iostream> #include <algorithm> #include <vector>  int main () { 
 std::vector<int> foo(5,10);
 std::vector<int> bar(5,33);
 // change the first parameter to get vector of differnt size
 std::cout << "BEFORE SWAP:\n";
 std::cout << "foo contains:";
 for (std::vector<int>::iterator it=foo.begin(); it!=foo.end(); ++it) {
  std::cout << ' ' << *it;
 }
 std::cout << '\n';

 std::cout << "bar contains:";
 for (std::vector<int>::iterator it=bar.begin(); it!=bar.end(); ++it)  {
  std::cout << ' ' << *it;
 }
 std::cout << '\n';

 std::swap_ranges(foo.begin(), foo.end(), bar.begin());

 std::cout << "AFTER SWAP:\n";
 std::cout << "foo contains:";
 for (std::vector<int>::iterator it=foo.begin(); it!=foo.end(); ++it)  {
  std::cout << ' ' << *it;
 }
 std::cout << '\n';

 std::cout << "bar contains:";
 for (std::vector<int>::iterator it=bar.begin(); it!=bar.end(); ++it)  {
  std::cout << ' ' << *it;
 }
 std::cout << '\n';

 return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `iter_swap`

`iter_swap`非常类似于 swap，但是`swap`改变两个元素的内容，`iter_swap`改变两个迭代器的内容。

你可以用前面的例子来做实验，我们只需要改变一行来去掉多余的参数，当然还要把`swap_ranges`改成`iter_swap`。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () { 

 std::vector<int> foo(5,10);
 std::vector<int> bar(5,33);
 // change the first parameter to get vector of differnt size
 std::cout << "BEFORE SWAP:\n";
 std::cout << "foo contains:";
 for (std::vector<int>::iterator it=foo.begin(); it!=foo.end(); ++it) {
   std::cout << ' ' << *it;
 }
 std::cout << '\n';

 std::cout << "bar contains:";
 for (std::vector<int>::iterator it=bar.begin(); it!=bar.end(); ++it)  {
  std::cout << ' ' << *it;
 }
 std::cout << '\n';

 std::iter_swap(foo.begin(), bar.begin());

 std::cout << "AFTER SWAP:\n";
 std::cout << "foo contains:";
 for (std::vector<int>::iterator it=foo.begin(); it!=foo.end(); ++it) {
   std::cout << ' ' << *it;
 }
 std::cout << '\n';

 std::cout << "bar contains:";
 for (std::vector<int>::iterator it=bar.begin(); it!=bar.end(); ++it)  {
  std::cout << ' ' << *it;
 }
 std::cout << '\n';

 return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

今天，我们看了一下对单个元素或容器执行移动或交换操作的算法。(嗯，技术上是在迭代器上)。

下次我们将开始学习变换算法。敬请期待！

*这篇文章最初发表在我的[博客](http://sandordargo.com/blog/2019/09/25/stl-alogorithms-tutorial-part-6-move-swap-operations)上。如果你有兴趣接收我的最新文章，请[注册我的简讯](http://eepurl.com/gvcv1j)和[在 Twitter 上关注我](https://twitter.com/SandorDargo)。*