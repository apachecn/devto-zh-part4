# 代码的出现——精灵看，精灵说——谜题 10

> 原文：<https://dev.to/10xlearner/advent-of-code-elves-look-elves-say-puzzle-10-25fd>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第十部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 10 日开始解决这个问题，名为“精灵看，精灵说”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/10)上找到，我在这里只描述问题的本质:

今天，小精灵们正在玩一个叫做`look-and-say`的游戏，我们将和他们一起玩。但是首先`look-and-say`游戏是关于什么的？它是迭代生成的序列，使用前一个值作为下一步的输入，为了生成下一个输入，你需要描述输入是什么。这个例子将使这一切变得清楚:

*   `1`变为`11`(数字`1`的 1 个副本)。
*   `11`变成了`21` ( `2`数字`1`的副本)。
*   `21`变成了`1211`(一个`2`后面跟着一个`1`)。
*   `1211`变成了`111221`(一个`1`，一个`2`，两个`1`)。
*   `111221`变成了`312211`(三个`1`，两个`2`，一个`1`)。

因此，对于这个问题，我们必须对给定的输入处理 40 次`look-and-say`步骤。

### 解

那我们玩吧！😄

显然，遵循单一责任原则，我们将有一个函数`lookAndSay`，它将对游戏进行一次迭代。但首先，让我们开始迭代过程，这更容易解释和理解，它看起来像这样:

```
constexpr auto numberOfProcessToRun = 40;
for(auto i = 0; i < numberOfProcessToRun ; ++i)
{
    input = lookAndSay(input); // with input a std::string either specified in the program or elsewhere
}
const auto result = input.size(); 
```

Enter fullscreen mode Exit fullscreen mode

太短了，你不觉得吗？不需要更久。事实上，我们迭代了 40 次，每次我们在输入变量中设置,`lookAndSay`函数的结果都与当前输入一起运行，因此它将为下一次迭代做好准备。最后，我们得到大小，因为这是我们想要的信息。

现在让我们来看看问题的核心，`lookAndSay`函数:

```
std::string lookAndSay(const std::string_view input)
{
    std::string result;
    size_t index = 0;
    while (index < input.size())
    {
        const auto firstDistinctElement = std::find_if(std::begin(input) + index, std::end(input),[&input, &index](const auto& character){
            return character != input[index];
        });
        const auto count = std::distance(std::begin(input) + index, firstDistinctElement);
        result += std::to_string(static_cast<size_t>(count)) + input[index];
        index += count;
    }
    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

有点复杂的^^

我们到底在这里做什么，我会解释给你听！

我们遍历输入，获取当前`index`处的字符，并在输入的下面寻找第一个不同的字符。

一旦我们找到它，我们就`count`当前`index`的字符和不同字符之间的距离，这就给出了相同字符的数量。

然后，我们将它和字符一起存储在结果变量中，直接转到不同的字符，重复这个过程，直到输入结束。

瞧，我们有了，我们可以玩看一看说一说的游戏😃

## 第二部分

这部分会很短！事实上，我们所要做的，除了我们要应用这个过程 50 次之外，完全一样。所以我们要做的，就是把`numberOfProcessToRun`的值从`40`改成`50`，这样做。你也可以让`numberOfProcessToRun`成为你程序的一个参数，这样你就可以对两个部分使用相同的二进制，就像我在我的 [GitHub](https://github.com/Xav83/AdventOfCode/tree/2015.10/2015/Day10) 上做的那样。

## 结论

你可以注意到，在这篇文章中写的解决方案，并没有包括所有使程序运行的资源，而只是解决这个问题的资源中有趣的部分。如果你想从头到尾看到节目，你可以上我的 [GitHub 账号](https://github.com/Xav83/AdventOfCode/tree/2015.10/2015/Day10)，探索完整的解决方案，如果你想添加评论或提问，在你阅读这篇文章的平台上，它也将帮助我提高我的文章质量。

以下是我们使用的 std 方法列表，我非常鼓励您查看它们的定义:

*   [std::string_view](https://en.cppreference.com/w/cpp/string/basic_string_view)
*   [std::string](https://en.cppreference.com/w/cpp/string/basic_string)
*   [std::find_if](https://en.cppreference.com/w/cpp/algorithm/find)
*   [标准::距离](https://en.cppreference.com/w/cpp/iterator/distance)
*   [std::begin](https://en.cppreference.com/w/cpp/iterator/begin)
*   [标准::结束](https://en.cppreference.com/w/cpp/iterator/end)
*   [std::to_string](https://en.cppreference.com/w/cpp/string/basic_string/to_string)

谢谢你的阅读，希望你喜欢😃

直到下一部分，享受学习和成长的乐趣。