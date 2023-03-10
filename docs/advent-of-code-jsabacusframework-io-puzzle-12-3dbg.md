# 代码的出现–jsabacusframework . io–难题 12

> 原文：<https://dev.to/10xlearner/advent-of-code-jsabacusframework-io-puzzle-12-3dbg>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第十二部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 12 日开始解决这个问题，命名为“JSAbacusFramework.io”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/12)上找到，我在这里只描述问题的本质:

圣诞老人的会计精灵在最近的订单后需要帮助平衡账目。您拥有的所有信息都是一个 JSON 文档，其中包含各种内容(数组、对象、数字和字符串)。

我们接下来要做的，就是找出所有的数字，然后求和。

### 解

首先，我们必须能够用 C++解析来自 JSON 的信息。我们可以从头开始编写一个 JSON 解析器来完成这项工作，但这将是一项漫长而艰巨的工作，我不知道您是否愿意，但我不想做已经做过的工作。所以我决定使用 nlohmann 的现代 C++ 库的 [JSON，它允许我们有一个易于阅读的代码来处理 JSON 文档。](https://github.com/nlohmann/json)

为了包含这个库，我使用了[柯南](https://conan.io/)和 [Cmake](https://cmake.org/) 的组合，但我不会在这里详述。你可以在 [Github 资源库](https://github.com/Xav83/AdventOfCode/tree/2015.12/2015/Day12)上看看。现在关于图书馆的一切都清楚了，让我们来解决这个问题。

首先，我们要准备好 JSON 文档以备使用:

```
#include <nlohmann/json.hpp>

std::ifstream file (pathToJsonDocument);
nlohmann::json jsonDocument;
file >> jsonDocument; 
```

Enter fullscreen mode Exit fullscreen mode

简单吧？！我们得到文件，然后只把它交给 JSON 库准备使用。

现在，我们如何得到 JSON 文档中所有数字的总和。正如问题中所解释的，JSON 文档中有 4 种类型的元素:数组、对象、数字和字符串。

所以我们必须检查 JSON 元素并定义如何处理它们。我们可以忽略这个字符串，因为其中没有数字。如果我们找到一个数，我们要做的就是把它加到总数上。但是更困难的部分是关于数组和对象。事实上，它们也可以包含对象类型的 for，这意味着我们的解决方案只有一个(悬念……)🥁:递归。

现在我们已经描述了我们要做的事情，让我们来看看做这件事的实际方法:

```
int getSum (const nlohmann::json& jsonDocument)
{
    auto sum{0};
    for (const auto& [key, value] : jsonDocument.items())
    {
        if(value.is_number())
        {
            sum += value.get<int>();
        }
        else if(value.is_array() || value.is_object())
        {
            sum += getSum (value);
        }
    }
    return sum;
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢 JSON 库让这个函数变得如此易读。如你所见，我们有一个对数组和对象的递归调用，每次我们找到一个数，我们就把它加到全局和中。这就是了。我们所要做的就是用我们创建的 JSON 调用这个方法，我们就好了。我们有解决问题的方法😃

## 第二部分

### 问题

会计精灵已经意识到他们重复计算了所有红色的东西。因此，我们现在必须忽略任何属性为“red”的对象(及其所有子对象)。只有对象，没有数组。

### 解

所以现在，我们不再忽略弦。通过在前面的方法中添加以下条件，我们将完成会计精灵的需求。

```
for (const auto& [key, value] : jsonDocument.items())
{
    // other conditions
    else if(value.is_string() && jsonDocument.is_object())
    {
        if(value.get<std::string>() == "red")
        {
            sum = 0;
            break;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们解释一下这个条件实际上是做什么的。我们首先查看元素是否是一个字符串，我们是否在一个对象中，这样我们就满足了标准的第一部分。如果是这种情况，我们查看字符串的值，然后我们满足最后一个要求，这样我们就可以停止将元素包含到数字的总和中。因此，我们首先将当前处理的总和重置为 0。这个总和是对象的总和，因此重置它也将避免将之前解析的元素添加到全局总和中，然后，我们从 for 循环中断开，不处理对象的其他元素。

瞧。我们有一个函数，可以让我们找到第二部分的解。😃

## 结论

你可以注意到，在这篇文章中写的解决方案，并没有包括所有使程序运行的资源，而只是解决这个问题的资源中有趣的部分。如果你想从头到尾看到节目，你可以上我的 [GitHub 账号](https://github.com/Xav83/AdventOfCode/tree/2015.12/2015/Day12)，探索完整的解决方案，如果你想添加评论或提问，在你阅读这篇文章的平台上，它也将帮助我提高我的文章质量。

以下是我们使用的元素列表，我非常鼓励您查看它们的定义:

*   [std::ifstream](https://en.cppreference.com/w/cpp/io/basic_ifstream)
*   [std::string](https://en.cppreference.com/w/cpp/string/basic_string)
*   [nlohmann::json](https://github.com/nlohmann/json)

谢谢你的阅读，希望你喜欢😃

直到下一部分，享受学习和成长的乐趣。