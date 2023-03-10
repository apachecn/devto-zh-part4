# 代码的出现——一夜之间——谜题 9

> 原文：<https://dev.to/10xlearner/advent-of-code-all-in-a-single-night-puzzle-9-1h42>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第九部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 9 日开始解决这个问题，名为“一夜之间”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/9)上找到，我在这里只描述问题的本质:

今年，圣诞老人有一些新的地点可以参观，这样他就可以在一个晚上送出所有的礼物。他的精灵已经为他提供了每一对地点之间的距离，我们需要用他们的信息找到他可以到达的最短距离，在每个城市经过一次？

例如，给定以下距离:

```
London to Dublin = 464
London to Belfast = 518
Dublin to Belfast = 141 
```

Enter fullscreen mode Exit fullscreen mode

其中最短的是`London -> Dublin -> Belfast = 605`，所以答案是`605`。

### 解

所以，如果我们从这个问题中去掉所有的圣诞词汇，我们将不得不在一个图中找到一条最短的路径，这条路径只经过每个节点一次。但首先，我们需要提取信息来构建图表！

为了提取城市，我基于“To”世界的位置和“=”，最后得到了这样的代码:

```
std::pair<City, City> getCitiesFromInstruction (const std::string_view instruction)
{
    const auto citySeparatorPosition = instruction.find(" to ");
    const auto equalSeparatorPosition = instruction.substr(citySeparatorPosition+4).find(" = ");
    return std::make_pair(City(instruction.substr(0, citySeparatorPosition)), City(instruction.substr(citySeparatorPosition+4, equalSeparatorPosition)));
} 
```

Enter fullscreen mode Exit fullscreen mode

而对于之间的距离，我使用了正则表达式，因为为什么不呢😛

```
Distance getDistanceFromInstruction (const std::string& instruction)
{
    std::regex word_regex("[0-9]+");
    auto words_begin = std::sregex_iterator(std::begin(instruction), std::end(instruction), word_regex);
    auto words_end = std::sregex_iterator();

    auto value{0};

    for (std::sregex_iterator i = words_begin; i != words_end; ++i)
    {
        std::smatch match = *i;
        return static_cast<Distance>(atoi(match.str().c_str()));
    }
    assert (false);
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了数据，我们可以在搜索最短路径之前将它们存储在图中。我不会详细说明我创建了什么类，我基本上为一个`City`，一个`Distance`，一个`Route`，一个`Graph`创建了一个类。

直接跳到主算法吧！

它由两个函数组成，一个函数`run`将创建我们在第二个函数中需要的元素，比如一个分类图，以及在调用第二个函数之前，我们已经访问过的城市和我们还需要访问的城市的一些存储。这个函数看起来像这样:

```
Distance run()
{
    std::vector<Node> citiesToVisit, citiesVisited;
    std::sort(std::begin(sortedGraph), std::end(sortedGraph));
    citiesToVisit = sortedGraph;
    return travel (citiesToVisit, 0, citiesVisited);
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，现在，我们必须访问的城市由排序图和组成，访问的城市是空的，因为我们还没有访问任何城市。您可能已经注意到，第二个函数名为`travel`，它有三个参数:我们还需要访问的城市、到目前为止已经走过的距离以及目前已经访问过的城市。这个函数是一个[递归函数](https://en.wikipedia.org/wiki/Recursion_(computer_science))，在这个函数中，我们将只查看一次经过所有城市的每条道路，计算行驶的距离，并与找到的最短距离进行比较，直到我们找出图中的最短路径。

说够了，让我们看看代码！

```
Distance travel(const std::vector<Node>& citiesToVisit, Distance distanceTraveledUntilNow, const std::vector<Node>& citiesVisited)
{
    if(citiesToVisit.empty())
    {
        return getRouteDistance();
    }
    auto min = std::numeric_limits<Distance>::max();
    for(const auto& city : citiesToVisit)
    {
        std::vector<Node> citiesVisitedWithNewCity, citiesVisitedWithNewCitySorted, lastCitiesToVisit;

        citiesVisitedWithNewCity = citiesVisited;
        citiesVisitedWithNewCity.emplace_back(city);

        citiesVisitedWithNewCitySorted = citiesVisitedWithNewCity;
        std::sort(std::begin(citiesVisitedWithNewCitySorted), std::end(citiesVisitedWithNewCitySorted));

        std::set_difference(std::begin(sortedGraph), std::end(sortedGraph), std::begin(citiesVisitedWithNewCitySorted), std::end(citiesVisitedWithNewCitySorted), std::back_inserter(lastCitiesToVisit));

        const auto distanceTraveled = travel (lastCitiesToVisit, distanceTraveledUntilNow, citiesVisitedWithNewCity);
        min = std::min(min, distanceTraveled);
    }

    return distanceTraveledUntilNow + min + getRouteDistance();
} 
```

Enter fullscreen mode Exit fullscreen mode

哇，这是一段相当长的代码。我们一点一点来解释吧。首先，我们有:

```
if(citiesToVisit.empty())
{
    return getRouteDistance();
} 
```

Enter fullscreen mode Exit fullscreen mode

这是`travel`方法的`stopping criterion`。这种情况允许递归方法停止。事实上，当我们不再有要访问的城市时，我们可以用`getRouteDistance`方法返回最后访问的两个城市的路线距离。

然后，我们有了 for 循环:

```
for(const auto& city : citiesToVisit)
{
    std::vector<Node> citiesVisitedWithNewCity, citiesVisitedWithNewCitySorted, lastCitiesToVisit;

    citiesVisitedWithNewCity = citiesVisited;
    citiesVisitedWithNewCity.emplace_back(city);

    citiesVisitedWithNewCitySorted = citiesVisitedWithNewCity;
    std::sort(std::begin(citiesVisitedWithNewCitySorted), std::end(citiesVisitedWithNewCitySorted));

    std::set_difference(std::begin(sortedGraph), std::end(sortedGraph), std::begin(citiesVisitedWithNewCitySorted), std::end(citiesVisitedWithNewCitySorted), std::back_inserter(lastCitiesToVisit));

    const auto distanceTraveled = travel (lastCitiesToVisit, distanceTraveledUntilNow, citiesVisitedWithNewCity);
    // ....
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些容器的操作都是递归的主要部分！它允许我们调用方法`travel`来调用自己，将一个旅行过的城市添加到已经旅行过的城市的容器中，并减少尚未访问的城市的数量。此外，for 循环使它成为，例如，我们用不同的旅行城市和旅行目的地集合调用`travel`方法，因为我们从旅行的城市中添加一个不同的城市到旅行的城市中(我让你读了几次，因为如果你没有递归方法的基础，这是不容易得到的)。

现在，由于这种递归方法，我们将能够`travel`通过图中只经过每个城市一次的所有路径。我们现在要做的就是找到最短的一个。这就是函数`travel`的最后一部分的作用:

```
auto min = std::numeric_limits<Distance>::max();
for(const auto& city : citiesToVisit)
{
    // ...
    const auto distanceTraveled = travel (lastCitiesToVisit, distanceTraveledUntilNow, citiesVisitedWithNewCity);
    min = std::min(min, distanceTraveled);
}

return distanceTraveledUntilNow + min + getRouteDistance(); 
```

Enter fullscreen mode Exit fullscreen mode

所以每次 travel 函数返回一个距离(或者因为它到达了`stopping criterion`或者因为上面的代码)，我们检查这个距离是否是所有进入 for 循环的`travel`调用中最短的。一旦 for 循环结束，我们就有了算法中该点的`travel`方法所遵循的最短路径距离，我们可以返回到目前为止所走过的距离(`travel`函数的一个参数)、for 循环中的`travel`方法调用的最短路径以及最后两个访问过的城市之间的距离之和。

这就是我们的完整算法。这个解释相当复杂，不容易得到，所以我只能鼓励你自己尝试修改它，看看会发生什么。你可以自己编码或者以我的代码为基础在我的 [GitHub](https://github.com/Xav83/AdventOfCode/tree/2015.09/2015/Day9) 上查看

现在，让我们看看第二部分给我们带来了什么😃

## 第二部分

### 问题

可以走最长的路，为什么要走最短的路？不是旅程比目的地更重要吗？😉

这个问题并没有被描述成这样，但是我们现在需要找出恰好经过每个城市一次的最长距离，因为圣诞老人想要它。

### 解

除了一句台词，一切都一模一样！你知道是哪一个吗？

…(危险音乐)

这样的悬念(或许没有😆)，所以要更改的行是计算 min 的行。事实上，通过调用一个`std::max`而不是一个`std::min`来改变它可以很好地完成这项工作。我很高兴它这么容易，因为第一部分一点也不容易！

如果我们想让我们的变量有一个正确的名字，我们最后的代码看起来应该是:

```
auto max = std::numeric_limits<Distance>::max();
for(const auto& city : citiesToVisit)
{
    // ...
    const auto distanceTraveled = travel (lastCitiesToVisit, distanceTraveledUntilNow, citiesVisitedWithNewCity);
    max = std::max(max, distanceTraveled);
}

return distanceTraveledUntilNow + max + getRouteDistance(); 
```

Enter fullscreen mode Exit fullscreen mode

这就是第二部分的解决方案😄

## 结论

你可以注意到，在这篇文章中写的解决方案，并没有包括所有使程序运行的资源，而只是解决这个问题的资源中有趣的部分。如果你想从头到尾看到节目，你可以上我的 [GitHub 账号](https://github.com/Xav83/AdventOfCode/tree/2015.09/2015/Day9)，探索完整的解决方案，如果你想添加评论或提问，在你阅读这篇文章的平台上，它也将帮助我提高我的文章质量。

以下是我们使用的 std 方法列表，我非常鼓励您查看它们的定义:

*   [标准::最大值](https://en.cppreference.com/w/cpp/algorithm/max)
*   [标准::最小](https://en.cppreference.com/w/cpp/algorithm/min)
*   [std::numeric_limits](https://en.cppreference.com/w/cpp/types/numeric_limits)
*   [std::set_difference](https://en.cppreference.com/w/cpp/algorithm/set_difference)
*   [std::vector](https://en.cppreference.com/w/cpp/container/vector)
*   [std::begin](https://en.cppreference.com/w/cpp/iterator/begin)
*   [标准::结束](https://en.cppreference.com/w/cpp/iterator/end)
*   [std::back_inserter](https://en.cppreference.com/w/cpp/iterator/back_inserter)
*   [std::sort](https://en.cppreference.com/w/cpp/algorithm/sort)
*   [std::regex](https://en.cppreference.com/w/cpp/regex)
*   [std::pair](https://en.cppreference.com/w/cpp/utility/pair)
*   [std::string_view](https://en.cppreference.com/w/cpp/string/basic_string_view)

谢谢你的阅读，希望你喜欢😃

直到下一部分，享受学习和成长的乐趣。