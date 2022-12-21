# 代码的出现-火柴杆-谜题 8

> 原文：<https://dev.to/10xlearner/advent-of-code-matchsticks-puzzle-8-3jb0>

# 代码的出现——火柴棒——谜题 8

你好！我是 Xavier Jouvenot，这是关于代码出现的[系列的第八部分。这里](https://adventofcode.com)可以找到上一部分

对于这个新帖子，我们将从 2015 年 12 月 8 日开始解决这个问题，名为“火柴棍”。我将在 C++中提出解决方案，但推理可以应用于其他语言。

## 第一部分

### 问题

这个问题的完整版可以直接在[降临代码网站](https://adventofcode.com/2015/day/8)上找到，我在这里只描述问题的本质:

今年，圣诞老人带来了他的电子版清单。他需要我们找出存放时会占用多少空间。为此，我们必须找出字符串文字的代码表示中的*字符数和内存中字符串*本身的*字符数之间的差异。*

例如:

*   " aaa"aaa\x27 "是 14 个字符的代码，但字符串本身包含 6 个" a "字符、一个单引号字符(用"")转义)和一个十六进制的`'`，所以内存中有 8 个字符，这意味着差的结果将是`14-8=6`。

作为参考，唯一使用的转义序列是`\\`(代表一个反斜杠)、`\"`(代表一个单独的双引号字符)和`\x`加上两个十六进制字符(代表一个带 ASCII 码的字符)

### 解

与前一个问题相比，这个问题更简单，所以解决起来会更快。先说程序的逻辑吧！

```
size_t totalNumberOfCharacter{0};
size_t inMemoryNumberOfCharacter{0};

foreachLineIn(fileContent, [&totalNumberOfCharacter, &encodedNumberOfCharacter](const std::string& line)
{
    totalNumberOfCharacter += getTotalNumberOfCharacters(line);
    inMemoryNumberOfCharacter += getNumberOfCharacterInMemory(line);
});

const auto result = totalNumberOfCharacter - inMemoryNumberOfCharacter; 
```

Enter fullscreen mode Exit fullscreen mode

简单对吗？！我们的目标是做减法，对于每一行，我们得到数据，最后做减法。

现在，我们需要做的就是查看函数`getTotalNumberOfCharacters`和`getNumberOfCharacterInMemory`的细节:

第一个(`getTotalNumberOfCharacters`)是一行程序，我们所要做的就是返回`line.size()`。

第二个更大，但仍然非常简单。事实上，为了找到字符串编码时的字符数，我们必须计数每个字母，并且对于字符的转义序列只计数一个。这个样子:

```
size_t size{0};
for(size_t index{0}; index < line.size(); ++index)
{
    ++size;

    if(index == line.size() - 1) { continue; }

    if(line[index] == '\\' && line[index + 1] == '\\') { ++index; }
    else if(line[index] == '\\' && line[index + 1] == '"') { ++index; }
    else if(line[index] == '\\' && line[index + 1] == 'x') { index += 3; }
}
return size - 2; // Subtracts the two " around the sequence of characters 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里，源代码允许我们给圣诞老人他的答案🎅

## 第二部分

### 问题

现在，让我们做相反的事情。除了找到ω，我们还将把每个代码表示编码为一个新字符串，并找到新编码表示的*个字符，包括周围的双引号，然后减去代码*的*个字符。*

例如:

*   " aaa"aaa\x27 "编码" " aaa\"aaa\x27 " "，增加了`7`个字符。

### 解

嗯，由于这部分与第一部分非常相似，我们可以重用代码，只需将对`getNumberOfCharacterInMemory`的调用替换为对函数`getNumberOfCharactersWhenEncoded`的调用。

那么让我们来看看这个新函数:

```
std::stringstream ss;
ss << std::quoted(line);
return ss.str().size(); 
```

Enter fullscreen mode Exit fullscreen mode

什么真的？！…的确，C++已经为此做了一些事情， [std::quoted](https://en.cppreference.com/w/cpp/io/manip/quoted) ，对字符序列进行编码。

从代码出现的那天起，我们就结束了。偶尔遇到一些简单的问题是很好的😃

## 结论

你可以注意到，在这篇文章中写的解决方案，并没有包括所有使程序运行的资源，而只是解决这个问题的资源中有趣的部分。如果你想从头到尾看这些节目，你可以登录我的 [GitHub 账户](https://github.com/Xav83/AdventOfCode/tree/2015.08/2015/Day8)，探索完整的解决方案，添加评论或提问。

以下是我们使用的 std 方法列表，我非常鼓励您查看它们的定义:

*   [标准::报价](https://en.cppreference.com/w/cpp/io/manip/quoted)

谢谢你的阅读，希望你喜欢😃

直到下一部分，享受学习和成长的乐趣。