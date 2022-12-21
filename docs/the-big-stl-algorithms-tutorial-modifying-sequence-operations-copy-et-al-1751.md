# STL 算法教程:修改序列操作。

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-modifying-sequence-operations-copy-et-al-1751>

在[大 STL 算法教程](http://sandordargo.com/blog/2019/01/30/stl-algos-intro)的下一部分，我们将发现所有名称以 copy 开头的修改序列操作:

*   `copy`
*   `copy_n`
*   `copy_if`
*   `copy_backward`

## `copy`

关于`std::copy`的目标没有什么大的惊喜。它获取输入范围的元素并将其复制到输出。这里举个例子:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto inputNumbers = std::vector<int>{1, 2, 3, 4, 5, 6};
  auto copiedNumbers = std::vector<int>{};
  std::copy(inputNumbers.begin(), inputNumbers.end(), copiedNumbers.begin());
  for (auto number : copiedNumbers) {
    std::cout << number << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

你觉得怎么样？我们的复制操作会成功吗？

不，不会的！相反，我们面临的是由分段故障导致的核心转储。原因是`copiedVectors`中根本没有足够的空间。它的大小为零，并且没有向量的自动扩展，除非你使用相应的 API(比如 [push_back()](http://www.cplusplus.com/reference/vector/vector/push_back/) )。

所以我们有两个选择。

1)我们可以确保输出向量有足够大的大小，例如通过用输入向量的大小来声明它:

```
auto copiedNumbers = std::vector<int>(inputNumbers.size()); 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有许多缺点。

*   `copiedNumbers`将用默认构造的对象填充。好的，在我们的例子中我们使用了整数，但是想象一下如果我们使用一个自定义对象的大向量，那么构建的成本会更高。

*   还有一个问题。如果在创建复制的数字和实际调用复制算法之间，输入的大小发生了变化，会怎么样呢？还是一样的分段断层。

2)相反，你可以使用一个*插入器*，它是一个插入器**迭代器**，顾名思义，它将帮助你向输出向量添加新元素。你可以这样用:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto inputNumbers = std::vector<int>{1, 2, 3, 4, 5, 6};
  auto copiedNumbers = std::vector<int>{};
  std::copy(inputNumbers.begin(), inputNumbers.end(), std::back_inserter(copiedNumbers));
  for (auto number : copiedNumbers) {
    std::cout << number << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们在示例中使用了`std::back_inserter`,它总是在目标的末尾插入新元素。就像`push_back`，但是你不能在算法中使用它，因为它与一个特定的容器相关，它不是一个插入迭代器。

您可能会想到的一个特殊问题是，我们的输出容器在开始时是空的，它会不断增长。有几个步骤？我们无法提前知道你正在使用的编译器的实现细节。但是，如果您的输入容器足够大，您可以假设输出操作符将在多个步骤中增长。调整向量的大小可能很昂贵，它需要内存分配，寻找连续的空闲区域，等等。

如果您想在这方面有所帮助，您可以使用`std::vector::reserve`，它将为 vector 保留足够大的内存区域，这样它就可以在没有新分配的情况下增长。如果预留的大小不够，就不会出现分段错误或其他任何问题，只是一个新的分配。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto inputNumbers = std::vector<int>{1, 2, 3, 4, 5, 6};
  auto copiedNumbers = std::vector<int>{};
  copiedNumbers.reserve(inputNumbers.size());
  std::copy(inputNumbers.begin(), inputNumbers.end(), std::back_inserter(copiedNumbers));
  for (auto number : copiedNumbers) {
    std::cout << number << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以观察到的是,`copy`不会自己插入新元素，但是它会覆盖输出容器中的现有元素。它只能在使用插入器迭代器时插入。

## `copy_n`

通过一对迭代器获取输入。一个标记输入范围的开始，一个标记输入范围的结束。但是如果你想复制比如说 5 个元素。简单，你仍然可以使用副本:

```
std::copy(inputNumbers.begin(), inputNumbers.begin()+5, std::back_inserter(copiedNumbers)); 
```

Enter fullscreen mode Exit fullscreen mode

指针算法在迭代器上工作得很好，所以你可以自由地这样做。但是你有一个更优雅的方法，你可以使用`copy_n`然后你只需要第一个迭代器:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto inputNumbers = std::vector<int>{1, 2, 3, 4, 5, 6};
  auto copiedNumbers = std::vector<int>();
  copiedNumbers.reserve(inputNumbers.size());
  std::copy_n(inputNumbers.begin(), 5, std::back_inserter(copiedNumbers));
  for (auto number : copiedNumbers) {
    std::cout << number << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

否则`copy_n`具有与`copy`相同的特征。

## `copy_if`

假设您只想复制列表中的某些元素。例如只有偶数？你能做什么？您可以简单地调用`copy_if`并以一元谓词的形式传递您的条件。会是什么呢？它可以是一个函数对象，一个函数指针或者仅仅是一个λ表达式。由于它的简单性，我坚持使用 lambdas:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto inputNumbers = std::vector<int>{1, 2, 3, 4, 5, 6};
  auto copiedNumbers = std::vector<int>();
  copiedNumbers.reserve(inputNumbers.size());
  std::copy_if(inputNumbers.begin(), inputNumbers.end(), std::back_inserter(copiedNumbers),  { return i % 2 == 0; });
  for (auto number : copiedNumbers) {
    std::cout << number << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `copy_backward`

今天最后一个算法是`copy_backward`。该算法从输入范围复制元素，但从后面开始向开始方向移动。

与输入相比，它是否产生了相反的顺序？不，不是的。它维持秩序。那么这个`copy_backward`为什么会存在呢？它有什么用？

想想下面这个案例。

你有一个输入范围`{1, 2, 3, 4, 5, 6, 7}`，你想复制部分`{1, 2, 3}`到`{2, 3, 4}`。为了更直观:

```
{1, 2, 3, 4, 5, 6, 7} => {1, 1, 2, 3, 5, 6, 7} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们尝试使用`copy`并且输出容器与输入相同。

你可以试试这个代码:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto inputNumbers = std::vector<int>{1, 2, 3, 4, 5, 6, 7};
  std::copy(std::begin(inputNumbers), std::begin(inputNumbers)+3, std::begin(inputNumbers)+1);
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
1
1
1
5
6
7 
```

Enter fullscreen mode Exit fullscreen mode

发生了什么事？

首先，将第一个数字(`inputNumbers.begin()`)复制到第二个数字(inputNumbers.begin()+1)上。所以 2 被 1 覆盖。然后第二个数字(`inputNumbers.begin()+1`)被复制到第三个(`inputNumbers.begin()+2`)位置。但此时，第二个数字是 1，所以这就是将被复制到第三个数字的内容。诸如此类。

*(有可能你正在使用一个足够聪明的编译器来克服这个问题)*

`std::copy_backward`会帮助你不要有这个问题。首先，它将复制输入范围的最后一个元素，然后它将一个接一个地向第一个元素移动，保持输出中的相对顺序。当向右复印并且输入范围与输出范围重叠时，使用`copy_backward`。

## 结论

今天，我们看了一下以复制前缀开始的算法。他们不是所有的复制算法，但其余的(像`reverse_copy`，`unique_copy`)我决定适合在其他部分。

也许要记住的最重要的事情是，如果你不想依赖你的编译器的聪明，并且你的输入和输出容器是相同的，你必须明智地考虑是应该使用`copy`还是`copy_backward`。

下次我们将开始学习移动和交换以及他们的朋友。敬请期待！

*这篇文章最初发表在我的[博客](http://sandordargo.com/blog/2019/08/14/stl-alogorithms-tutorial-part-5-copy-operations)上。如果你有兴趣接收我的最新文章，请[注册我的简讯](http://eepurl.com/gvcv1j)和[在 Twitter 上关注我](https://twitter.com/SandorDargo)。*