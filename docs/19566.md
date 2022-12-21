# 代码的出现——我被告知不会有数学——难题 2

> 原文：<https://dev.to/10xlearner/advent-of-code-i-was-told-there-would-be-no-math-puzzle-2-3jd7>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第二部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将解决 2015 年 12 月 2 日的第二个问题，名为“我被告知不会有数学”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/2)上找到，我在这里只描述问题的本质:

圣诞老人的小精灵们需要为礼物订购更多的包装纸。要做到这一点，我们知道我们需要为每件礼物准备足够的包装纸来覆盖礼物盒的表面，再加上一些额外的包装纸，大约是礼物盒最小的一面。提醒一下，礼物盒子的表面积是`2*l*w + 2*w*h + 2*h*l`，其中`l`是盒子的长度，`w`是宽度，`h`是高度。

例如:

*   一份尺寸为`2x3x4`的礼物需要`2*6 + 2*12 + 2*8 = 52`平方英尺的包装纸加上`6`平方英尺的松弛部分，总共`58`平方英尺。

### 解

```
#include <algorithm>

auto wrappingPaperArea{0};
foreachLineIn(fileContent, [&wrappingPaperArea](const auto& line)
{
    const auto dimension = makeDimensionFromLine(line);
    const auto areas = dimension.toAreas();
    const auto min = std::min_element(std::begin(areas), std::end(areas));
    wrappingPaperArea += 2 * areas[0] + 2 * areas[1] + 2 * areas[2] + *min;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个源代码展示了解决方案的主要部分。该解决方案包括 3 个部分。

首先，我们必须从输入中获取信息。为此，我们使用我们已经创建的方法`foreachLineIn`和`makeDimensionFromLine`以及结构`Dimension`来存储数据。`foreachLineIn`允许我们一次处理每一行输入、每一个呈现。`makeDimensionFromLine`从文本中提取描述当下的维度转化为可用的数据。

然后，我们可以用尺寸计算面积，用`Dimension::toAreas()`和`std::min_element`知道哪个面积最小。

最后，我们计算出礼物所需的包装纸的数量。

我鼓励你去我的 [Github](https://github.com/Xav83/AdventOfCode/tree/master/2015/Day2) 上看看这个代码示例中使用的方法的实现。

而且，这次不出意外，问题的第二部分

## 第二部分

### 问题

现在，圣诞老人的精灵们的丝带快用完了，因为丝带都是一样宽的，我们只需要找到他们需要订购的长度。为了确定这个长度，我们有以下信息:

*   包装礼物所需的缎带是环绕其边的最短距离。
*   圣诞老人的精灵还需要用丝带做一个蝴蝶结，这个蝴蝶结的体积大约是现在的立方英尺。

对于**零件 2** 中尺寸`2x3x4`相同的礼物，我们必须订购`2+2+3+3 = 10`英尺的丝带来包裹礼物，加上`2*3*4 = 24`英尺的蝴蝶结丝带，总共`34`英尺。

### 解

```
auto ribbonLength{0};
foreachLineIn(fileContent, [&ribbonLength](const auto& line)
{
    const auto dimension = makeDimensionFromLine(line);
    const auto max = std::max({dimension.length, dimension.width, dimension.height});
    ribbonLength += 2 * dimension.length + 2 * dimension.width + 2 * dimension.height - 2 * max + dimension.length * dimension.width * dimension.height;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个源代码也展示了解决方案的主要部分。

和上一部分一样，我们得到了礼物的尺寸。然后，我们可以从计算中减去最大值，而不是找出当前尺寸中最小的两个来计算其边的最短距离。这样做，我们从上面得到一个礼物的丝带的计算。

## 结论

你可以注意到，这个帖子里写的解决方案，并没有包括所有制作运行程序的源码，而只是解决这个问题的源码中有趣的部分。如果你想从头到尾看这些节目，你可以登录我的 [GitHub 账户](https://github.com/Xav83/AdventOfCode/tree/master/2015/Day2)，探索完整的解决方案，添加评论或提问。

以下是我们使用的 std 方法列表，我非常鼓励您查看它们的定义:

*   [std::min_element](https://en.cppreference.com/w/cpp/algorithm/min_element)
*   [标准::最大值](https://en.cppreference.com/w/cpp/algorithm/max)

谢谢你的阅读，希望你喜欢😃。直到下一部分，享受学习和成长的乐趣。