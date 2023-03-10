# 大 STL 算法教程:其余的非修改序列操作

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-the-rest-of-non-modifying-sequence-operations-5c7e>

在 STL 算法教程的下一部分，我们将会发现所有我们还没有看到的非修改序列操作。

也就是说，我们将深入了解以下功能:

*   `count`
*   `count_if`
*   `equal`
*   `mismatch`
*   `is_permutation`

## `count`

名字本身就说明了一切，对吧？`count`接受一个迭代器范围，作为第三个参数，它接受一个在传入范围中寻找的值。就这么简单

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto count = std::count(myvector.begin(), myvector.end(), 1);
  std::cout << "Number of occurences of '1' in myvector: " << count;

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，答案是 2。

## `count_if`

`count_if`不同于`count`，就像`find_if`不同于`find`一样。就像`count`(或`find`)一样，它接受一系列迭代器，但不是一个值作为第三个参数，而是接受一个一元谓词，并通过向它传递列表中的每个元素来返回谓词对`true`求值的次数。

一元谓词可以是函数对象、指向函数的指针或 lambda 函数。您应该使用哪一种取决于您的使用案例。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto count = std::count_if(myvector.begin(), myvector.end(), {return number % 2 == 0;});
  std::cout << "Number of even numbers in myvector: " << count;

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，答案还是两个。但是在`count`的例子中，数字`1`被计数两次，这里`2`和`4`被计数为偶数。

## `equal`

`equal`函数返回一个布尔值，其值取决于两个范围的所有元素是否相等。这是简单的解释，但生活可能会有所不同。

对于只接受参数的构造函数，肯定是这样的。前两个迭代器定义一个范围，第三个参数定义另一个范围的开始。让我们举一个简单的例子，我们有两个内容相同的向量:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto otherVector(myvector);
  if (std::equal(myvector.begin(), myvector.end(), otherVector.begin())) {
      std::cout << "The two vectors are equal\n ";
  } else {
      std::cout << "The two vectors are NOT equal\n ";
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，我们的两个向量相等。在我们的下一个例子中，我们在第二个向量的开头插入一个元素，我们试图通过不传递`begin()`迭代器，而是传递`begin()+1`的迭代器来忽略它。应该会发生什么？

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto otherVector(myvector);
  otherVector.insert(otherVector.begin(),42);
  if (std::equal(myvector.begin(), myvector.end(), otherVector.begin()+1)) {
      std::cout << "The two vectors are equal\n ";
  } else {
      std::cout << "The two vectors are NOT equal\n ";
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在不匹配的元素之后开始比较，因此如果在第二个向量中我们有与原始向量中相同数量的元素，并且这些元素匹配，`equal()`也将这样说。

那么，如果在向量的末尾插入相同的元素，我们从头开始比较，会发生什么呢？

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto otherVector(myvector);
  otherVector.push_back(42);
  if (std::equal(myvector.begin(), myvector.end(), otherVector.begin())) {
      std::cout << "The two vectors are equal\n ";
  } else {
      std::cout << "The two vectors are NOT equal\n ";
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

等于将返回`true`！什么？那么 equal 又是干什么的呢？它检查第一个传递的范围是否是从第三个参数定义的指定点开始的第二个范围的一部分。它不检查两个容器是否相等。为此，您可以简单地比较两个容器。

还有第二个构造函数，通过它可以传递一个二元谓词作为两个范围的比较器。否则它以同样的方式工作。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto otherVector(myvector);
  otherVector.push_back(42);
  if (std::equal(myvector.begin(), myvector.end(), otherVector.begin(), {return i==j;})) {
      std::cout << "The two vectors are equal\n ";
  } else {
      std::cout << "The two vectors are NOT equal\n ";
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `mismatch`

`mismatch`和`equal`挺像的。它还公开了两个构造函数，你可以根据你想要比较两个范围的方式在它们之间进行选择，你传递的方式和你传递“相等”的方式相同。

区别在于`equal`返回一个整数，而 mismatch 返回一对迭代器。指向第一个不匹配位置的第一个和第二个范围的迭代器。

在失败的情况下，所以在没有不匹配的情况下，第一个范围的迭代器指向它的最后一个元素，而第二个迭代器指向第二个范围，位置与第一个相同。因此，如果两个范围相等，则两个点都在最后一个元素之后。当第一个范围是第二个范围的一部分，但第二个范围更长时，第二个迭代器指向不在第一个范围内的第一个元素。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 4, 5};
  auto otherVector = std::vector<int>{1, 2, 3, 42, 5};
  auto result = std::mismatch(myvector.begin(), myvector.end(), otherVector.begin(), {return i==j;});
  std::cout << "Mismatching elements are " << *result.first << " and " << *result.second << "\n";
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `is_permutation`

`is_permutation`也类似于`equal`。它提供了两个构造函数，都接受两个范围，第一个由它的开始和结束定义，而另一个只由它的开始点定义。正如我们在`equal`和`mismatch`中看到的，`is_permutation`也接受一个可选的二元谓词，用于比较第一个和第二个范围的元素。

与`equal`一样，`is_permutation`也返回一个布尔值，如果所有元素都匹配，该值将为`true`。但是对于`is_permutation`来说，顺序并不重要。如果两个查询范围包含相同的元素，而不管它们的位置，它将返回`true`。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 1, 4, 5};
  auto otherVector(myvector);
  std::random_shuffle(otherVector.begin(), otherVector.end());
  if (std::is_permutation(myvector.begin(), myvector.end(), otherVector.begin())) {
      std::cout << "The two vectors are permutations of each other\n ";
  } else {
      std::cout << "The two vectors are NOT permutations of each other\n ";
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

关于 random_shuffle 的更多内容将在后面的另一篇文章中介绍，但是考虑到它的名字，你可以放心地假设它将对一个向量的元素进行洗牌。

## 结论

在本文中，我们已经讨论完了`<algorithm>`头的非修改序列操作。我们看到了`count`、`count_if`、`equal`、`mismatch`和`is_permutation`是如何工作的。

下次我们将开始学习修改序列操作。敬请期待！

*这篇文章最初发表在我的[博客](http://sandordargo.com/blog/2019/07/24/.stl-algorithm-tutorial-part-4-rest-of-non-modifying)上。如果你有兴趣接收我的最新文章，请[注册我的简讯](http://eepurl.com/gvcv1j)和[在 Twitter 上关注我](https://twitter.com/SandorDargo)。*