# 代码的出现——真空中完美的球形房屋——谜题 3

> 原文：<https://dev.to/10xlearner/advent-of-code-perfectly-spherical-houses-in-a-vacuum-puzzle-3-n78>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第三部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将解决 2015 年 12 月 3 日的第二个问题，名为“真空中的完美球形房屋”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/3)上找到，我在这里只描述问题的本质:

圣诞老人正在给一个无限的二维房屋网格送礼物。他首先在出发地点给房子送去一份礼物，然后北极的一个精灵通过无线电告诉他下一步该去哪里。他一次移动一个房子，向北(`^`)、向南(`v`)、向东(`>`)或向西(`<`)。每次圣诞老人拜访一户人家，他都会送一份礼物，但是，给圣诞老人下达指示的小精灵喝了太多的蛋酒，使得圣诞老人不止一次拜访各家。所以，我们必须找出**有多少家庭收到至少一份礼物**。

例如:

*   让圣诞老人给`3`家送去一份礼物，这是他开始的那一家，以及从那里向东的两家。
*   让圣诞老人给各家各户送去一堆礼物。

### 解

在这里，我将描述我用来得出解决方案的思考过程。

首先，我们必须知道圣诞老人在房屋网格上的位置，这意味着我们需要他的**坐标**。

```
struct Coordinate
{
    int x{0};
    int y{0};
};

Coordinate santaPosition; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了圣诞老人的位置，我们必须存储圣诞老人走过的路。描述这条路径最简单的方式是一个`Coordinates`的**列表**。

```
#include <vector>

using Path = std::vector<Coordinate>
Path santaPath; 
```

Enter fullscreen mode Exit fullscreen mode

我们终于有了处理这个问题的所有结构。现在，我们必须找到圣诞老人走的路。

```
// Adds the house where Santa start to deliver presets
santaPath.emplace_back(santaPosition);

for(auto direction : input)
{
    // Modify Santa's location
    switch (direction)
    {
        case '^': ++santaPosition.y; break;
        case '>': ++santaPosition.x; break;
        case '<': --santaPosition.x; break;
        case 'v': --santaPosition.y; break;
    }
    // Adds the new location to Santa's path
    santaPath.emplace_back(santaPosition);
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们有圣诞老人走过的路。7 我们现在需要做的是找出圣诞老人走过的路上有多少不同的房子。为此，我们将使用两种标准算法: [std::sort](https://en.cppreference.com/w/cpp/algorithm/sort) 和 [std::unique](https://en.cppreference.com/w/cpp/algorithm/unique) 。

```
#include <algorithm>

std::sort(std::begin(santaPath), std::end(santaPath));
auto it = std::unique(std::begin(santaPath), std::end(santaPath));
const auto numberOfHousesVisited = std::distance(std::begin(santaPath), it); 
```

Enter fullscreen mode Exit fullscreen mode

注意，为了能够使用那些算法，我们必须指定结构`Coordinate`的操作符`==`和`<`。

我鼓励你去我的 [GitHub](https://github.com/Xav83/AdventOfCode/tree/2015.03/2015/Day3) 上看看这个代码样本中使用的完整解决方案。

## 第二部分

### 问题

这个问题和第一部分一样，只是现在，有两个“送货员”，圣诞老人和机器人圣诞老人。两者都从相同的位置开始，并根据 elf 的指令轮流移动。我们还得弄清楚有多少房子收到了至少一份礼物。

例如:

*   给各家送礼物，因为圣诞老人去北方，然后机器人圣诞老人去南方。

### 解

大部分源代码与第 1 部分非常相似，所以我们将只关注不同之处。首先，我们可以为圣诞老人和机器人圣诞老人集成一个公共结构，我称之为`DeliveryMan`。

```
struct DeliveryMan
{
    Coordinate position;
    Path path;
};

DeliveryMan santa, roboSanta; 
```

Enter fullscreen mode Exit fullscreen mode

在收集指令时，我们可以在接收指令的送货员和三元指令之间切换。

```
DeliveryMan deliveryMan;

deliveryMan = deliveryMan == &santa ? &roboSanta: &santa; 
```

Enter fullscreen mode Exit fullscreen mode

最后，一旦我们排序了圣诞老人和机器人圣诞老人路径，我们必须在使用 std::unique 之前**合并**它们。

```
std::vector<Coordinate> mergedPath;
    std::merge(std::begin(santa.path), std::end(santa.path), std::begin(roboSanta.path), std::end(roboSanta.path), std::back_inserter(mergedPath)); 
```

Enter fullscreen mode Exit fullscreen mode

这部分有趣的地方就在这里。你可以在我的 [GitHub](https://github.com/Xav83/AdventOfCode/tree/2015.03/2015/Day3) 上查看这段代码的完整解决方案。

## 结论

你可以注意到，这个帖子里写的解决方案，并没有包括所有制作运行程序的源码，而只是解决这个问题的源码中有趣的部分。如果你想从头到尾看完节目，可以上我的【GitHub 账号】[https://GitHub . com/xav 83/AdventOfCode/tree/2015.03/2015/day 3](https://github.com/Xav83/AdventOfCode/tree/2015.03/2015/Day3)，探索完整解决方案，如果你愿意可以添加评论或提问。

这里是我们使用过的 std 方法和容器的列表，我非常鼓励你去看看它们的定义:

*   [std::vector](https://en.cppreference.com/w/cpp/container/vector)
*   [std::sort](https://en.cppreference.com/w/cpp/algorithm/sort)
*   [std::unique](https://en.cppreference.com/w/cpp/algorithm/unique)
*   [标准::距离](https://en.cppreference.com/w/cpp/iterator/distance)
*   [std::merge](https://en.cppreference.com/w/cpp/algorithm/merge)
*   [std::back_inserter](https://en.cppreference.com/w/cpp/iterator/back_inserter)

感谢您的阅读，希望您喜欢 [![😃](img/1a8a82559b31687e49e2968a76ad7283.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M8HQryyi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f603.png)

直到下一部分，享受学习和成长的乐趣。