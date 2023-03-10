# 准则的出现——公司政策——难题 11

> 原文：<https://dev.to/10xlearner/advent-of-code-corporate-policy-puzzle-11-1676>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第 11 部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 11 日开始解决这个问题，名为“公司政策”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/11)上找到，我在这里只描述问题的本质:

北极的密码有了新政策，圣诞老人的密码刚刚过期。为了记住他的新密码，圣诞老人重复增加他的旧密码串，直到它有效。增量就像数数一样:`xx`、`xy`、`xz`、`ya`、`yb`等等。将最右边的字母增加一个步长；如果是 z，它会绕到 a，并对左边的下一个字母重复，直到一个字母不绕回为止。

该策略规定密码必须符合以下要求:对圣诞老人来说不幸的是，一个新的安全精灵最近开始了，他强加了一些额外的密码要求:

*   密码必须有八个字母
*   密码必须包括一个至少三个字母的递增直线，如`abc`。
*   密码不得包含字母`i`、`o`或`l`，这些字母容易混淆。
*   密码必须包含至少两对不同的、不重叠的字母，如`aa`。

比如`abcdefgh`之后的下一个密码是`abcdffaa`。现在，我们必须找到圣诞老人的新密码。

### 解

首先，我们必须能够“增加”密码。的确，一个角色天生没有这样的行为，所以我们必须创造出自己的`PasswordCharacter` :

```
class PasswordChar
{
public:
    // ... others functions

    void increment()
    {
        value = willWrapAroundToA () ? 'a' : value + 1;
    }

    bool willWrapAroundToA () const { return value == 'z'; }

private:
    char value{'a'};
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您只有对本练习感兴趣的方法。所以我们有一个可以递增的`PasswordChar`,当它到达字母“z”时，它将会回绕，我们将能够得到这个信息，以便能够递增下一个字母。

现在我们有了一个可以递增的字符，我们需要一个由几个这样的字符组成的密码，它也可以递增。

```
class Password
{
    // ... other functions

    void increment ()
    {
        for(int index {static_cast<int>(value.size()) - 1}; index >= 0 ; --index)
        {
            const auto characterWillWrap = value[index].willWrapAroundToA();
            value[index].increment();
            if(!characterWillWrap)
            {
                break;
            }
        }
    }

private:
    std::array<PasswordChar, 8> value;
}; 
```

Enter fullscreen mode Exit fullscreen mode

他来了，密码！为了确保它符合第一个要求，我们将使用大小为`8`的`std::array`。我们也有一个递增函数，当被调用时，它将递增密码的每个字母，从末尾到开头，但停止递增是一个字符在递增后换行。

很好，现在我们有了一个可以递增的密码，它符合第一个要求。然后，我们要做的就是确保密码是安全的，这意味着它符合最后三个要求，我们就完成了。遵循单一责任原则，我们将有三个函数(实际上是一些方法，因为我已经将它们作为密码类的一部分)。

先说最简单的，禁书。

```
bool Password::hasNoConfusingLetters() const
{
    return std::none_of(std::begin(value), std::end(value), 
    {
        return passwordChar == 'i' || passwordChar == 'o' || passwordChar == 'l';
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

性病让事情变得简单了，你不觉得吗？这个 std 函数`none_of`将返回*的`true`，`value`中的元素在传递给 lambda 函数时都不会返回`true`。这么有用！我喜欢它😍*

好了，现在，让我们看看下一个需求，三个字母的递增直线

```
bool Password::hasIncreasingStraight() const
{
    for(auto index = size_t{0}; index < value.size() - 2; ++index)
    {
        if(value[index] == value[index + 1] - 1 && value[index] == value[index + 2] - 2)
        {
            return true;
        }
    }
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

遗憾的是，这个没有 std 算法，但仍然非常简单。我们必须小心边界，确保我们停留在字符数组中，不像我第一次写这个函数时那样😝

最后是最后一个要求，两个不重叠的线对:

```
bool Password::hasTwoNonOverlappingPairs() const
{
    bool hasAtLeastOnePair{false};
    for(auto index = size_t{0}; index < value.size() - 1; ++index)
    {
        if(value[index] == value[index + 1])
        {
            if(hasAtLeastOnePair)
            {
                return true;
            }
            hasAtLeastOnePair = true;
            ++index;
        }
    }
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

这本书远不容易阅读。所以我要再详细解释一下。

我们正在检查密码。我们检查当前字母和下一个字母之间是否有一对(所以我们在最后一个字母前停下来以避免超出数组)。如果这不是一对，我们就看下一个字母，但如果是一对，就有趣了。我们检查一下，我们已经找到了一对。如果这是一个案例，那它，我们符合要求。但如果不是，那就意味着这是我们发现第一对。因此，我们登记这个信息，并跳过下一个字母，以避免找到重叠的对。

现在，我们可以用&&运算符将这些功能组合成一个名为`isSafe`的函数，从而很容易知道密码是否安全。

现在，我们可以增加一个密码，并检查密码是否符合要求。我们所要做的，就是结合这些特征，为圣诞老人找到下一个安全的密码

```
Password newPassword (oldPassword);
do
{
    newPassword.increment();
} while (!newPassword.isSafe()); 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。在这个循环结束时，我们已经为圣诞老人准备好了密码😄🎆

## 第二部分

这一部分我们将快速进行，因为要解决的问题是一样的。的确，在这一部分，我们需要找到上一部分已经找到的密码的下一个密码。

就我个人而言，我已经直接将第一部分的密码传递给了同一个程序，以找到这一部分的解决方案。但是如果你愿意，你也可以通过运行两次`do-while`循环来修改程序使它找到第二个下一个密码。

我鼓励你们看看我的 [GitHub](https://github.com/Xav83/AdventOfCode/tree/2015.11/2015/Day11) 上的解决方案，看看我是怎么做的，因为我使用 [CMake](https://cmake.org/) 来构建和检查我找到的解决方案。

## 结论

你可以注意到，在这篇文章中写的解决方案，并没有包括所有使程序运行的资源，而只是解决这个问题的资源中有趣的部分。如果你想从头到尾看到节目，你可以上我的 [GitHub 账号](https://github.com/Xav83/AdventOfCode/tree/2015.11/2015/Day11)，探索完整的解决方案，如果你想添加评论或提问，在你阅读这篇文章的平台上，它也将帮助我提高我的文章质量。

以下是我们使用的 std 方法列表，我非常鼓励您查看它们的定义:

*   [std::none_of](https://en.cppreference.com/w/cpp/algorithm/all_any_none_of)
*   [std::array](https://en.cppreference.com/w/cpp/container/array)
*   [std::begin](https://en.cppreference.com/w/cpp/iterator/begin)
*   [标准::结束](https://en.cppreference.com/w/cpp/iterator/end)

谢谢你的阅读，希望你喜欢😃

直到下一部分，享受学习和成长的乐趣。