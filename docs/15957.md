# 《代码的出现》——他没有实习生吗？谜题 5

> 原文：<https://dev.to/10xlearner/advent-of-code-doesn-t-he-have-intern-elves-for-this-puzzle-5-5735>

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第三部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 5 日开始解决这个问题，名为“他没有实习生精灵吗？”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

但是首先，我想解决前一个问题的一个要点，但是如果你想跳到第五天的问题，请便。

## 第 4 回一天

Reddit 用户 TheFlamefire 给了我一些很好的意见，帮助我改进了对[第四天问题](//2019-06-21-AdventOfCode2015-Day4-The-Ideal-Stocking-Stuffer.md)的解决方案。他的评论与[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)产生了直接共鸣，这也是我想在第五天的问题中谈论的(很好的巧合😄).

### 单一责任原则，那是什么？

正如[维基百科](https://en.wikipedia.org/wiki/Single_responsibility_principle)所描述的:

> 单一责任原则是一种计算机编程原则，它规定每个模块、类或函数都应该对软件提供的功能的单一部分负责，…

通过应用这个原则，你将得到一个更加模块化的代码，代码块更小，更容易理解，任何人都可以阅读和使用。采用这个原则还有很多其他很大的好处，这里就不描述了。此外，这个原则是[坚实原则](https://en.wikipedia.org/wiki/SOLID)的第一部分，值得几个博客帖子来正确解释它。

### 单日责任原则 4

在第 4 天的解决方案中，我们创建了一个名为`getMd5`的函数，它将私钥传递给 MD5 算法**和**，并将结果从数组`unsigned char`转换为`std::string`。我强调的“和”是可疑的，因为它表明该函数做了两件事，这违反了单一责任原则。为了纠正它，我必须将`getMd5`函数分成两个函数:

*   `getMd5`它只返回由私钥生成的 MD5 哈希对应的字节数组。
*   `bytesToString`将一个数组`unsigned char`转换成一个`std::string`

我只能鼓励你去看看我的 [Github](https://github.com/Xav83/AdventOfCode/tree/2015.05/2015/Day4) 上那些功能的实现，现在，让我们进入第 5 天！😈

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/5)上找到，我在这里只描述问题的本质:

圣诞老人需要帮助来找出他的文本文件中哪些字符串是顽皮的，哪些是善良的。好的字符串具有以下所有属性:

*   它至少包含三个元音
*   它至少包含一个连续出现两次的字母
*   它不包含字符串`ab`、`cd`、`pq`或`xy`

### 解

和第 4 天的观点一样，对于这个问题，我们将使用[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)。事实上，这个问题非常适合引入它，因为好的字符串属性列表中的每个项目符号都可以放在一个函数中，承担一个责任。所以我们最终有三个功能:

*   `bool hasThreeVowels(const std::string_view str)`
*   `bool hasLettersAppearingTwiceInRow(const std::string_view str)`
*   `bool hasForbiddenSubString (const std::string_view str)`

让我们更深入地挖掘这些功能。

首先对于`hasThreeVowels`函数，我们可以直接使用`std::count_if`算法得到正确的行为，比如:

```
 return 3 <= std::count_if(std::begin(str), std::end(str), 
    {
        return letter == 'a' || letter == 'e' || letter == 'i' || letter == 'o' ||letter == 'u';
    }); 
```

Enter fullscreen mode Exit fullscreen mode

为了遵守单一责任原则，我们可以创建一个函数`isVowel`,而不是在这里使用的 lambda 中直接包含条件。

对于第二个函数`hasLettersAppearingTwiceInRow`，我没有找到一个 std 算法让我有一个方便的解决方案，所以我使用了一个简单的解决方案，使用了一个基于范围的循环:

```
char previousCharacter = CHAR_MIN;
for(const auto& character : str)
{
    if(character == previousCharacter) { return true; }
    previousCharacter = character;
}
return false; 
```

Enter fullscreen mode Exit fullscreen mode

对于最后一个函数`hasForbiddenSubString`，我们可以使用`std::string_view::find`方法:

```
 return str.find("ab") != std::string_view::npos ||
           str.find("cd") != std::string_view::npos ||
           str.find("pq") != std::string_view::npos ||
           str.find("xy") != std::string_view::npos; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了这三个函数，我们还有两件事要做。首先，我们必须创建一个函数来定义什么是好的字符串，通过在一个条件中组合前面的函数:

```
bool isNice(const std::string_view str)
{
    return hasThreeVowels(str) &&
           hasLettersAppearingTwiceInRow(str) &&
           ! hasForbiddenSubString(str);
} 
```

Enter fullscreen mode Exit fullscreen mode

并将此函数应用于每行输入:

```
auto numberOfNiceString{0};
foreachLineIn(fileContent, [&numberOfNiceString](const std::string& str)
{
    if(isNice(str))
    {
        ++numberOfNiceString;
    }
});
std::cout << numberOfNiceString; 
```

Enter fullscreen mode Exit fullscreen mode

瞧，我们有一个漂亮的解决方案，它在寻找圣诞老人漂亮的绳子时应用了单一责任原则。👼

## 第二部分

### 问题

实际上，圣诞老人犯了一些错误，用来寻找淘气和可爱的绳子的标准是错误的。事实上，一个好的字符串具有以下属性:

*   它包含在字符串中至少出现两次且没有重叠的任意两个字母的对
*   它包含至少一个字母，两个字母之间正好有一个字母重复

### 解

该死，所以你告诉我我们必须再做一次？！😮

实际上没有。因为我们应用了单一责任原则，我们程序的逻辑保持不变，我们只需要通过使用两个新函数来修改`isNice`方法，每个函数对应一个好字符串的属性:

```
bool isNice(const std::string_view str)
{
    return hasTwiceAPairOfLetters(str) &&     
           hasOneLetterAppearingTwiceWithOnlyOneLetterBetween(str);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们要做的就是实现这些功能。首先，我们来看看`hasTwiceAPairOfLetters`实现:

```
for(size_t i = 0; i < str.size() - 2; ++i)
{
    std::string_view str_toSearch(&str[i], 2); // The first 2 elements of the string
    std::string_view str_toSearchIn(&str[i + 2], str.size() - i - 2); // The rest of the string
    if(str_toSearchIn.find(str_toSearch) != std::string_view::npos)
    {
        return true;
    }
}
return false; 
```

Enter fullscreen mode Exit fullscreen mode

对于这个实现我没有太多要说的，除了`std::string_view`做得非常好😃

现在，函数`hasOneLetterAppearingTwiceWithOnlyOneLetterBetween` :

```
for(size_t i = 0; i < str.size() - 2; ++i)
{
    if(str[i] == str[i + 2]) { return true; }
}
return false; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们现在有了一个让圣诞老人满意的漂亮绳子的定义🎅

## 结论

和往常一样，你可以注意到，这篇文章中写的解决方案，并没有包括所有使程序运行的源码，而只是解决这个问题的源码中有趣的部分。如果你想从头到尾看这些节目，你可以登录我的 [GitHub 账户](https://github.com/Xav83/AdventOfCode/tree/2015.05/2015/Day5)，探索完整的解决方案，添加评论或提问。

这里是我们使用过的 std 方法和容器的列表，我非常鼓励你去看看它们的定义:

*   [std::string_view](https://en.cppreference.com/w/cpp/string/basic_string_view)
*   [std::string_view::find](https://en.cppreference.com/w/cpp/string/basic_string_view/find)
*   [STD::string _ view::NPO](https://en.cppreference.com/w/cpp/string/basic_string_view/npos)
*   [std::count_if](https://en.cppreference.com/w/cpp/algorithm/count)

感谢你的阅读，希望你喜欢。对我来说，我喜欢这样做😃

直到下一部分，享受学习和成长的乐趣。