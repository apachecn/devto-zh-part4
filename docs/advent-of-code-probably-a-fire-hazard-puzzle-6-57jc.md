# 代码的出现——可能是火灾隐患——难题 6

> 原文：<https://dev.to/10xlearner/advent-of-code-probably-a-fire-hazard-puzzle-6-57jc>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第六部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 6 日开始解决这个问题，名为“可能是火灾隐患”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/6)上找到，我在这里只描述问题的本质:

你想在节日房屋装饰比赛中击败你的邻居。为此，您需要部署一个 1000×1000 的灯光网格，并按照圣诞老人的指示来显示理想的照明配置。

有 3 种指令:

*   **打开** `0,0`到`999,999`会打开(或保持打开)每一盏灯。
*   **切换** `0,0`到`999,0`将切换第一行的 1000 盏灯，关闭那些亮着的，打开那些关着的。
*   **关闭** `499,499`到`500,500`会关闭(或离开)中间的四个灯。

### 解

下面是解决这个问题的算法的主要部分:

```
 foreachLineIn(fileContent, [&grid](const std::string& line)
    {
        const auto actionId = getActionFromLine(line);
        const auto rectangle = getRectangleFromLine(line);
        switch (actionId)
        {
        case LightAction::TURN_ON:
            grid.turnOn(rectangle);
            break;
        case LightAction::TURN_OFF:
            grid.turnOff(rectangle);
            break;
        case LightAction::TOGGLE:
            grid.toggle(rectangle);
            break;
        default:
            assert(false);
            break;
        }
    });

     std::cout << grid.getNumberOfLightTunedOn(); 
```

Enter fullscreen mode Exit fullscreen mode

用简单的英语来说，这个代码意味着，对于每个 In 指令，我们识别指令的种类，在网格上关注并应用指令。

剩下的代码非常简单，可以在我的 GitHub 上找到。

作为总结，我可以告诉你还有五个要素:

```
enum class LightAction; // Qualifies which kind of instruction we are dealing with
class Coordinate { int x, y; } // Object representing a position on a grid (use in Day 3)
class Rectangle { Coordinate topLeft, bottomRight; } // Object representing an area
class Light { bool isOn{false}; }; // Object representing one light of the grid
class Grid { std::array<std::array<Light>> lights; } ; // Object representing the grid of lights 
```

Enter fullscreen mode Exit fullscreen mode

## 第二部分

### 问题

实际上，你误译了圣诞老人给你的说明(原始版本为古代北欧精灵语)。这些说明的真正含义是:

*   **打开**意味着你应该把那些灯的亮度增加`1`。
*   **切换**意味着你应该将那些灯的亮度增加`2`。
*   **关闭**意味着你应该将那些灯的亮度降低`1`。

### 解

好了，对于这一部分，代码的结构基本相同，只是实现细节和部分函数的命名(从`getNumberOfLightTurnedOn`到`getBrightness`)有所变化，我就不赘述了，你可以在我的 [GitHub](https://github.com/Xav83/AdventOfCode/tree/2015.06) 上找到这一部分的解决方案。

```
// Part One
class Light { bool isOn{false}; };
// Part Two
class Light { size_t brightness{0}; }; 
```

Enter fullscreen mode Exit fullscreen mode

其实我遇到过一个没想到的有趣问题。事实上，我是在 Windows 上开发代码的，在编译过程中我遇到了一个问题。只有在`Light`类中添加一个整数(代表亮度)时，我才会得到奇怪的错误消息。

经过一番挖掘，发现来自于栈大小，默认太少。为了修复这个问题，我添加了链接选项 [/STACK](https://docs.microsoft.com/fr-fr/cpp/build/reference/stack-stack-allocations?view=vs-2019) ，并将其设置为 10 MO(而不是默认的 1)，所有东西都重新编译一遍(youhou \o/)。

## 结论

和往常一样，你可以注意到，这篇文章中写的解决方案，并没有包括所有使程序运行的源码，而只是解决这个问题的源码中有趣的部分。如果你想从头到尾看这些节目，你可以登录我的 [GitHub 账户](https://github.com/Xav83/AdventOfCode/tree/2015.0-6/2015/Day6)，探索完整的解决方案，添加评论或提问。

感谢你的阅读，希望你喜欢。对我来说，我喜欢这样做😃

直到下一部分，享受学习和成长的乐趣。