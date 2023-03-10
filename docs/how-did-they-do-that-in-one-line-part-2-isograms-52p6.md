# 他们是如何在一行中做到的？第 2 部分:等值线图

> 原文：<https://dev.to/ekand/how-did-they-do-that-in-one-line-part-2-isograms-52p6>

我刚刚解决了 Codewars 上的另一个挑战，就像我之前的[帖子](https://dev.to/erikkristoferanderson/how-did-they-do-all-that-in-one-line-a-codewars-challenge-explanation-3e2c)中一样，最流行的解决方案使用的代码比我的少得多。

这个[形](https://www.codewars.com/kata/isograms)的挑战是获取一个字符串并指出它是否是一个等值线。等值图是一个没有重复字母的单词。

下面是流行的解决方案:

```
def is_isogram(string):
    return len(string) == len(set(string.lower())) 
```

Enter fullscreen mode Exit fullscreen mode

这是我的

```
def is_isogram(string):
    result = False
    string = string.lower()
    my_list = []
    for character in string:
        if character in my_list:
            return False
        my_list.append(character)
    return True 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。差别很大。

让我们来分析一下那个简短的解决方案。

```
def is_isogram(string):
    return len(string) == len(set(string.lower())) 
```

Enter fullscreen mode Exit fullscreen mode

等式测试的右边很简单。只是绳子的长度。右边的密度更大一点，但实际上也没那么糟糕。它的功能是计算`string`中唯一元素集合的长度。

让我们看看它是怎么做到的。

首先，`string.lower`是必要的，因为同一个字母可能有大写和小写的情况，程序应该将这些识别为同一个字母。

接下来，`set(string.lower())`给了我们一个集合数据类型，包含了 string 中所有的唯一元素。也就是说，如果字母`b`在 string 中出现两次，那么它在 set 中只会出现一次。

然后`len(set(string.lower()))`计算这个集合的长度。

我们现在准备考虑`==`比较。如果字符串的长度等于集合的长度，函数将返回`True`，表示`string`是一个等值线图。另一方面，如果集合的长度不同于字符串的长度，这意味着至少一个字母在字符串中出现至少两次。该函数将返回`False`，因为`string`不是等值线图。

最后一点，考虑一下`string`是空字符串的情况是很有趣的。在这种情况下，集合的长度和字符串的长度都为零，因此该函数将返回 True。

幸运的是，指令表明我们应该假设一个空字符串是一个等值线。

那就方便了。这意味着我们不需要额外的代码来处理空字符串的情况。功能体停留在简洁的一行。

这就是了。我用 8 行函数体解决的一个挑战，只用 1 行就能完成。